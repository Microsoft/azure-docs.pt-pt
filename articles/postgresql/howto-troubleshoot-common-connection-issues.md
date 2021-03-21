---
title: Conexões de resolução de problemas - Base de dados Azure para PostgreSQL - Servidor Único
description: Saiba como resolver problemas de ligação à Base de Dados Azure para PostgreSQL - Servidor Único.
keywords: conexão pós-quadrado,cadeia de ligação,problemas de conectividade,erro transitório,erro de ligação
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: bff930153dc8941fbfe561edf963d5b1c1e7811f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014623"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>Problemas de conexão de resolução de problemas para Azure Database para PostgreSQL - Servidor Único

Os problemas de ligação podem ser causados por uma variedade de coisas, incluindo:

* Definições de firewall
* Tempo de intervalo de ligação
* Informações incorretas de login
* Limite máximo atingido em algumas bases de dados Azure para recursos postgresQL
* Problemas com a infraestrutura do serviço
* Manutenção a ser realizada no serviço
* A atribuição de cálculo do servidor é alterada escalando o número de vCores ou movendo-se para um nível de serviço diferente

Geralmente, os problemas de ligação à Base de Dados Azure para PostgreSQL podem ser classificados da seguinte forma:

* Erros transitórios (de curta duração ou intermitente)
* Erros persistentes ou não transitórios (erros que se repetem regularmente)

## <a name="troubleshoot-transient-errors"></a>Resolver erros transitórios

Erros transitórios ocorrem quando a manutenção é realizada, o sistema encontra um erro com o hardware ou software, ou altera o nível de vCores ou de serviço do seu servidor. A Base de Dados Azure para o serviço PostgreSQL tem uma elevada disponibilidade incorporada e foi concebida para mitigar este tipo de problemas automaticamente. No entanto, a sua aplicação perde a ligação ao servidor por um curto período de tempo de menos de 60 segundos, no máximo. Alguns eventos podem ocasionalmente demorar mais tempo a mitigar, como quando uma grande transação causa uma recuperação prolongada.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Medidas para resolver problemas transitórios de conectividade

1. Verifique o Painel de Serviços do [Microsoft Azure](https://azure.microsoft.com/status) para saber se ocorreram falhas conhecidas durante o período em que os erros foram reportados pela aplicação.
2. As aplicações que se ligam a um serviço na nuvem, como o Azure Database for PostgreSQL, devem esperar erros transitórios e implementar lógica de retíria para lidar com estes erros em vez de os surgir como erros de aplicação para os utilizadores. Rever [manuseamento de erros de conectividade transitórios para a Base de Dados Azure para PostgreSQL](concepts-connectivity.md) para as melhores práticas e orientações de design para lidar com erros transitórios.
3. À medida que um servidor se aproxima dos seus limites de recursos, os erros podem parecer ser problemas transitórios de conectividade. Ver [Limitações na Base de Dados Azure para PostgreSQL](concepts-limits.md).
4. Se os problemas de conectividade continuarem, ou se a duração durante a qual a sua aplicação se encontra exceder 60 segundos ou se vir múltiplas ocorrências do erro num determinado dia, arquive um pedido de suporte Azure selecionando **Obter Suporte** no site de [Suporte Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Resolver erros persistentes

Se a aplicação não ligar persistentemente à Base de Dados Azure para PostgreSQL, geralmente indica um problema com um dos seguintes:

* Configuração da firewall do servidor: Certifique-se de que a base de dados Azure para firewall do servidor PostgreSQL está configurada para permitir ligações do seu cliente, incluindo servidores de procuração e gateways.
* Configuração da firewall do cliente: A firewall do seu cliente deve permitir ligações ao servidor de base de dados. Endereços IP e portas do servidor que não pode ser permitido, bem como nomes de aplicações como PostgreSQL em algumas firewalls.
* Erro do utilizador: Pode ter parâmetros de ligação mal definidos, como o nome do servidor na cadeia de ligação ou um *\@ sufixo* de nome de servidor em falta no nome de utilizador.
* Se vir o _error Server não está configurado para permitir ligações ipv6_, note que o nível Básico não suporta pontos finais de serviço VNet. Tem de remover o ponto final do Microsoft.Sql da sub-rede que está a tentar ligar-se ao servidor Basic.
* Se vir o erro de ligação _do valor "***" inválido quando o suporte SSL não é compilado por_ engano, significa que o seu cliente PostgreSQL não suporta SSL. Provavelmente, o libpq do lado do cliente não foi compilado com a bandeira "-com-openssl". Por favor, tente conectar-se com um cliente PostgreSQL que tenha suporte SSL. 

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Medidas para resolver problemas de conectividade persistentes

1. Configurar [regras de firewall](howto-manage-firewall-using-portal.md) para permitir o endereço IP do cliente. Apenas para testes temporários, crie uma regra de firewall utilizando 0.0.0.0 como endereço IP inicial e utilizando o endereço IP inicial e utilizando o endereço IP inicial 255.255.255.255 como o endereço IP final. Isto abrirá o servidor a todos os endereços IP. Se isto resolver o seu problema de conectividade, remova esta regra e crie uma regra de firewall para um endereço IP ou intervalo de endereços devidamente limitado.
2. Em todas as firewalls entre o cliente e a internet, certifique-se de que a porta 5432 está aberta para ligações de saída.
3. Verifique a sua cadeia de ligação e outras definições de ligação.
4. Verifique a saúde do serviço no painel de instrumentos. Se você acha que há uma paralisação regional, consulte [a visão geral da continuidade do negócio com a Azure Database for PostgreSQL](concepts-business-continuity.md) para as medidas para recuperar para uma nova região.

## <a name="next-steps"></a>Passos seguintes

* [Tratamento de erros de conectividade transitórios para a Base de Dados de Azure para PostgreSQL](concepts-connectivity.md)

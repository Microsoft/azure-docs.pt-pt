---
title: Visão geral - Base de Dados Azure para MariaDB
description: Conheça a Base de Dados Azure para o serviço MariaDB, um serviço de base de dados relacional na nuvem da Microsoft com base na edição comunitária do MySQL.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 3/18/2020
ms.openlocfilehash: 8115625099543d378728a6313a8cc4c95fec0cd2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662022"
---
# <a name="what-is-azure-database-for-mariadb"></a>O que é a Azure Database for MariaDB?

O Azure Database for MariaDB é um serviço de base de dados relacional na cloud da Microsoft. A Azure Database for MariaDB baseia-se na [edição comunitária MariaDB](https://mariadb.org/download/) (disponível sob a licença GPLv2) motor de base de dados, versão 10.2 e 10.3.

O Azure Database for MariaDB proporciona:

- Elevada disponibilidade incorporada sem custo adicional.
- Desempenho previsível, com preços pay as you go inclusivos.
- Dimensionamento conforme necessário, em alguns segundos.
- Proteção segura de dados confidenciais inativos e em movimento.
- Cópias de segurança automáticas e recuperação para um ponto anterior no tempo até 35 dias.
- Segurança e conformidade de nível empresarial.

Estas capacidades quase não exigem administração. São oferecidas sem custos adicionais. O Azure Database for MariaDB pode ajudá-lo a desenvolver a sua aplicação e acelerar o tempo de comercialização rapidamente. Não tem de alocar tempo precioso e recursos para gerir máquinas virtuais e infraestruturas. Também pode continuar a desenvolver a sua aplicação ao utilizar as ferramentas de código aberto e a plataforma da sua preferência. Entregue com a velocidade e eficiência que o seu negócio precisa, sem ter de aprender novas competências.

Para saber mais sobre os principais conceitos e funcionalidades no Azure Database for MariaDB, incluindo desempenho, escalabilidade e capacidade de gestão, veja estes inícios rápidos:

- [Criar um servidor do Azure Database for MariaDB com o portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Criar um servidor do Azure Database for MariaDB com a CLI do Azure](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajuste o desempenho e dimensione em segundos

A Base de Dados Azure para o serviço MariaDB oferece vários níveis de serviço: Básico, Propósito Geral e Memória Otimizada. Cada escalão oferece desempenho e capacidades diferentes para suportar desde cargas de trabalho de base de dados leves até pesadas. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica ajuda a base de dados a responder de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Consulte [Escalões de preço](concepts-pricing-tiers.md) para obter mais detalhes.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

Como decidir quando aumentar ou reduzir verticalmente? Pode utilizar a monitorização de desempenho incorporada e as funcionalidades de alerta do Azure Database for MariaDB, juntamente com as classificações de desempenho baseadas em vCores. Com estas ferramentas, pode avaliar rapidamente o efeito do dimensionamento vertical ou horizontal dos vCores com base nas suas necessidades de desempenho atuais ou estimadas. Consulte [Alertas](howto-alert-metric.md) para obter mais detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha a sua aplicação e o seu negócio operacionais

A disponibilidade de 99,99% da Azure, a SLA, é alimentada por uma rede global de datacenters geridos pela Microsoft. A rede ajuda a manter a sua aplicação operacional 24 horas por dia, sete dias por semana. Beneficia da segurança incorporada, tolerância a falhas e proteção de dados no Azure Database for MariaDB. Com Azure Database for MariaDB, pode utilizar o restauro para um ponto anterior no tempo para restaurar um estado anterior de um servidor, até 35 dias.

## <a name="secure-your-data"></a>Proteger os dados

Os serviços de base de dados do Azure têm uma tradição de segurança de dados mantida pelo Azure Database for MariaDB. O Azure Database for MariaDB oferece funcionalidades que limitam o acesso, protegem os dados inativos e ativos, e ajudam a monitorizar a atividade. Visite o [Centro de Fidedignidade do Azure](https://www.microsoft.com/trustcenter/security) para obter informações sobre a segurança da plataforma do Azure. Para obter mais informações sobre a Base de Dados Azure para funcionalidades de segurança MariaDB, consulte a [visão geral](concepts-security.md)de segurança .

## <a name="contacts"></a>Contactos

Pode enviar todas as dúvidas ou sugestões que possa ter sobre como trabalhar com o Azure Database for MariaDB para a [Equipa da Azure Database for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com), (não para um alias de suporte técnico).

Também pode utilizar os seguintes pontos de contacto:
- Para contactar o Suporte do Azure, [abra um pedido de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) no portal do Azure.
- Para corrigir um problema na sua conta, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através nos [Fóruns de Comentários do Azure](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Passos seguintes

Agora que já leu uma introdução ao Azure Database for MariaDB, está pronto para:
- Consulte [a](https://azure.microsoft.com/pricing/details/mariadb/) página de preços para comparar custos e calculadoras. 
- Começa por [criar o teu primeiro servidor.](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->

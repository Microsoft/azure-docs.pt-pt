---
title: Descrição geral do serviço de base de dados relacional da Azure Database for MariaDB
description: Learn about the Azure Database for MariaDB service, a relational database service in the Microsoft cloud based on the MySQL community edition.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: aed2e090a1e3a6a4c80f70ba25da9a903a6192c1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484145"
---
# <a name="what-is-azure-database-for-mariadb"></a>O que é a Azure Database for MariaDB?

O Azure Database for MariaDB é um serviço de base de dados relacional na cloud da Microsoft. Azure Database for MariaDB is based on the [MariaDB community edition](https://mariadb.org/download/) (available under the GPLv2 license) database engine, version 10.2 and 10.3.

A Azure Database for MariaDB fornece:

- Elevada disponibilidade incorporada sem custos adicionais.
- Desempenho previsível com preços de pay as you go, inclusive.
- Dimensionamento conforme preciso em segundos.
- Proteção segura dos dados confidenciais inativos e em movimento.
- Cópias de segurança automáticas e restauro para um ponto anterior no tempo até 35 dias.
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

The Azure Database for MariaDB service offers several service tiers: Basic, General Purpose, and Memory Optimized. Cada escalão oferece desempenho e capacidades diferentes para suportar desde cargas de trabalho de base de dados leves até pesadas. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica ajuda a base de dados a responder de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. See [Pricing tiers](concepts-pricing-tiers.md) for details.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

Como decidir quando aumentar ou reduzir verticalmente? Pode utilizar a monitorização de desempenho incorporada e as funcionalidades de alerta do Azure Database for MariaDB, juntamente com as classificações de desempenho baseadas em vCores. Com estas ferramentas, pode avaliar rapidamente o efeito do dimensionamento vertical ou horizontal dos vCores com base nas suas necessidades de desempenho atuais ou estimadas. Consulte [Alertas](howto-alert-metric.md) para obter mais detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha a sua aplicação e o seu negócio operacionais

Azure's industry-leading 99.99% availability SLA is powered by a global network of Microsoft-managed datacenters. A rede ajuda a manter a sua aplicação operacional 24 horas por dia, sete dias por semana. Beneficia da segurança incorporada, tolerância a falhas e proteção de dados no Azure Database for MariaDB. Com Azure Database for MariaDB, pode utilizar o restauro para um ponto anterior no tempo para restaurar um estado anterior de um servidor, até 35 dias.

## <a name="secure-your-data"></a>Proteja os seus dados

Os serviços de base de dados do Azure têm uma tradição de segurança de dados mantida pelo Azure Database for MariaDB. O Azure Database for MariaDB oferece funcionalidades que limitam o acesso, protegem os dados inativos e ativos, e ajudam a monitorizar a atividade. Visite o [Centro de Fidedignidade do Azure](https://www.microsoft.com/trustcenter/security) para obter informações sobre a segurança da plataforma do Azure. For more information about Azure Database for MySQL security features, see the [security overview](concepts-security.md).

## <a name="contacts"></a>Contactos

Pode enviar todas as dúvidas ou sugestões que possa ter sobre como trabalhar com o Azure Database for MariaDB para a [Equipa da Azure Database for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com), (não para um alias de suporte técnico).

Também pode utilizar os seguintes pontos de contacto:
- Para contactar o Suporte do Azure, [abra um pedido de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) no portal do Azure.
- Para corrigir um problema na sua conta, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através nos [Fóruns de Comentários do Azure](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Passos seguintes

Agora que já leu uma introdução ao Azure Database for MariaDB, está pronto para:
- Veja a página de [preços](https://azure.microsoft.com/pricing/details/mariadb/) para ver as comparações de preços e as calculadoras. 
- Comece por [criar o seu primeiro servidor](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->

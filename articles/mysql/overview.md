---
title: Overview of Azure Database for MySQL relational database service
description: Learn about the Azure Database for MySQL service, a relational database service in the Microsoft cloud based on the MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 680b5e9ef8e7e8ed59d3b502b49fc1b45d016e80
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483511"
---
# <a name="what-is-azure-database-for-mysql"></a>O que é a Base de Dados do Azure para MySQL?

Azure Database for MySQL is a relational database service in the Microsoft cloud based on the [MySQL Community Edition](https://www.mysql.com/products/community/) (available under the GPLv2 license) database engine, versions 5.6, 5.7, and 8.0. Azure Database for MySQL delivers:

- Elevada disponibilidade incorporada sem custos adicionais.
- Desempenho previsível com preços de pay as you go, inclusive.
- Dimensionamento conforme necessário em segundos.
- Segurança para proteger os dados confidenciais inativos e em movimento.
- Cópias de segurança automáticas e restauro para um ponto anterior no tempo até 35 dias.
- Segurança e conformidade de nível empresarial.

Estas funcionalidades não exigem praticamente qualquer ação de administração e são todas fornecidas sem custos adicionais. Permitem-lhe concentrar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de colocação no mercado, em vez de alocar tempo e recursos valiosos à gestão de máquinas virtuais e de infraestruturas. Além disso, pode continuar a desenvolver a sua aplicação, com a plataforma e as ferramentas open-source da sua preferência, e garantir a velocidade e a eficiência exigidas pelo seu negócio sem ter de adquirir novas competências.

![Azure Database for MySQL conceptual diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

This article is an introduction to Azure Database for MySQL core concepts and features related to performance, scalability, and manageability, with links to explore details. Veja estes guias de introdução para começar:

- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Para obter um conjunto de amostras da CLI do Azure, veja:

- [Azure CLI samples for Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajuste o desempenho e dimensione em segundos
The Azure Database for MySQL service offers several service tiers: Basic, General Purpose, and Memory Optimized. Cada escalão oferece desempenho e capacidades diferentes para suportar desde cargas de trabalho de base de dados leves até pesadas. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. See [Pricing tiers](concepts-service-tiers.md) for details.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Como decidir quando aumentar e reduzir verticalmente? Utilize a monitorização de desempenho incorporada e as funcionalidades de alerta juntamente com as classificações de desempenho baseadas em vCores. Com estas ferramentas, pode avaliar rapidamente o impacto do dimensionamento vertical ou horizontal dos vCores com base nas suas necessidades de desempenho atuais ou estimadas. Consulte [Alertas](howto-alert-on-metric.md) para obter mais detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha a sua aplicação e o seu negócio operacionais
O contrato de nível de serviço (SLA) do Azure líder da indústria, que garante 99,99% de disponibilidade, com tecnologia de uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação operacional 24 horas por dia, sete dias por semana. With every Azure Database for MySQL server, you take advantage of built-in security, fault tolerance, and data protection that you would otherwise have to buy or design, build, and manage. With Azure Database for MySQL, you can use point-in-time restore to recover a server to an earlier state, as far back as 35 days.

## <a name="secure-your-data"></a>Proteja os seus dados
Azure database services have a tradition of data security that Azure Database for MySQL upholds, with features that limit access, protect data at-rest and in-motion, and help you monitor activity. Visite o [Centro de Fidedignidade do Azure](https://www.microsoft.com/trustcenter/security) para obter informações sobre a segurança da plataforma do Azure. For more information about Azure Database for MySQL security features, see the [security overview](concepts-security.md).

## <a name="contacts"></a>Contactos
For any questions or suggestions you might have about working with Azure Database for MySQL, send an email to the Azure Database for MySQL Team ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). This email address is not a technical support alias.

Além disso, considere os seguintes pontos de contacto, conforme adequado:

- Para contactar o Suporte do Azure, [crie um pedido no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Passos seguintes
Now that you've read an introduction to Azure Database for MySQL and answered the question "What is Azure Database for MySQL?" you're ready to:

- Consulte a página de preços para ver as comparações de preços e as calculadoras. [Preços](https://azure.microsoft.com/pricing/details/mysql/)
- Comece por criar o seu primeiro servidor. [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)

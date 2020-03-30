---
title: Visão geral - Base de Dados Azure para MySQL
description: Conheça a Base de Dados Azure para o serviço MySQL, um serviço de base de dados relacional na nuvem da Microsoft com base na Edição Comunitária MySQL.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 8f49811ad0d40c70933d32227cfb17a5144b857a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067812"
---
# <a name="what-is-azure-database-for-mysql"></a>O que é a Base de Dados do Azure para MySQL?

Azure Database for MySQL é um serviço de base de dados relacional na nuvem da Microsoft baseado no motor de base de dados [MySQL Community Edition](https://www.mysql.com/products/community/) (disponível sob a licença GPLv2), versões 5.6, 5.7 e 8.0. A Base de Dados Azure para MySQL entrega:

- Elevada disponibilidade incorporada sem custos adicionais.
- Desempenho previsível com preços de pay as you go, inclusive.
- Dimensionamento conforme necessário em segundos.
- Segurança para proteger os dados confidenciais inativos e em movimento.
- Cópias de segurança automáticas e restauro para um ponto anterior no tempo até 35 dias.
- Segurança e conformidade de nível empresarial.

Estas funcionalidades não exigem praticamente qualquer ação de administração e são todas fornecidas sem custos adicionais. Permitem-lhe concentrar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de colocação no mercado, em vez de alocar tempo e recursos valiosos à gestão de máquinas virtuais e de infraestruturas. Além disso, pode continuar a desenvolver a sua aplicação, com a plataforma e as ferramentas open-source da sua preferência, e garantir a velocidade e a eficiência exigidas pelo seu negócio sem ter de adquirir novas competências.

![Base de Dados Azure para diagrama conceptual MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Este artigo é uma introdução à Base de Dados Azure para conceitos e funcionalidades centrais do MySQL relacionadas com desempenho, escalabilidade e capacidade de gestão, com links para explorar detalhes. Veja estes guias de introdução para começar:

- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Para obter um conjunto de amostras da CLI do Azure, veja:

- [Exemplo da CLI do Azure para a Base de Dados do Azure para MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajuste o desempenho e dimensione em segundos
A Base de Dados Azure para o serviço MySQL oferece vários níveis de serviço: Básico, Propósito Geral e Otimização da Memória. Cada escalão oferece desempenho e capacidades diferentes para suportar desde cargas de trabalho de base de dados leves até pesadas. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Consulte os [níveis de preços](concepts-service-tiers.md) para obter mais detalhes.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Como decidir quando aumentar e reduzir verticalmente? Utilize a monitorização de desempenho incorporada e as funcionalidades de alerta juntamente com as classificações de desempenho baseadas em vCores. Com estas ferramentas, pode avaliar rapidamente o impacto do dimensionamento vertical ou horizontal dos vCores com base nas suas necessidades de desempenho atuais ou estimadas. Consulte [Alertas](howto-alert-on-metric.md) para obter mais detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha a sua aplicação e o seu negócio operacionais
O contrato de nível de serviço (SLA) do Azure líder da indústria, que garante 99,99% de disponibilidade, com tecnologia de uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação operacional 24 horas por dia, sete dias por semana. Com todas as Bases de Dados Azure para servidor MySQL, aproveita-se da segurança incorporada, da tolerância a falhas e da proteção de dados que de outra forma teria de comprar ou projetar, construir e gerir. Com a Base de Dados Azure para MySQL, pode utilizar um restauro pontual para recuperar um servidor para um estado anterior, desde 35 dias.

## <a name="secure-your-data"></a>Proteger os dados
Os serviços de base de dados Azure têm uma tradição de segurança de dados que a Azure Database for MySQL defende, com funcionalidades que limitam o acesso, protegem os dados em repouso e em movimento, e ajudam a monitorizar a atividade. Visite o [Centro de Fidedignidade do Azure](https://www.microsoft.com/trustcenter/security) para obter informações sobre a segurança da plataforma do Azure. Para obter mais informações sobre a Base de Dados Azure para funcionalidades de segurança MySQL, consulte a [visão geral](concepts-security.md)da segurança .

## <a name="contacts"></a>Contactos
Para quaisquer perguntas ou sugestões que possa ter sobre trabalhar com a Base de Dados Azure para o MySQL, envie um e-mail para a Base de Dados Azure para a MySQL Team ([ @Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Este endereço de e-mail não é um pseudónimo técnico.

Além disso, considere os seguintes pontos de contacto, conforme adequado:

- Para contactar o Suporte do Azure, [crie um pedido no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Passos seguintes
Agora que leu uma introdução à Base de Dados Azure para o MySQL e respondeu à pergunta "O que é a Base de Dados Azure para o MySQL?" Está pronto para:

- Consulte a página de preços para ver as comparações de preços e as calculadoras. [Preços](https://azure.microsoft.com/pricing/details/mysql/)
- Comece por criar o seu primeiro servidor. [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Construa a sua primeira aplicação usando o seu idioma preferido: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java Ruby](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)

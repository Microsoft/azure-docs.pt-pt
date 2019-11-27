---
title: Descrição geral do serviço de base de dados relacional da Base de Dados do Azure para PostgreSQL
description: Fornece uma descrição geral da Base de Dados do Azure para o serviço de base de dados relacional PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 9ea0610811f6906526afe55d577e04a8decd5f49
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481660"
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é a Base de Dados do Azure para PostgreSQL?
O banco de dados do Azure para PostgreSQL é um serviço de banco de dados relacional no Microsoft Cloud criado para desenvolvedores. Ele se baseia na versão da Comunidade do mecanismo de banco de dados [PostgreSQL](https://www.postgresql.org/) de software livre e está disponível em duas opções de implantação: servidor único e hiperescala (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Banco de dados do Azure para PostgreSQL-servidor único
A opção de implantação de servidor único fornece:

- [Alta disponibilidade](concepts-high-availability.md) interna sem custo adicional (99,99% de SLA)
- Desempenho previsível com preços pay as you go inclusivos
- [Escala vertical conforme necessário](concepts-pricing-tiers.md) em segundos
- [Monitoramento e alertas](concepts-monitoring.md) para avaliar seu servidor
- Segurança e conformidade de nível empresarial
- [Protegido para proteger](concepts-security.md) dados confidenciais em repouso e em movimento
- [Backups automáticos e restauração pontual](concepts-business-continuity.md) por até 35 dias


Todas estas funcionalidades não exigem praticamente qualquer ação de administração e são fornecidas sem custos adicionais. Eles permitem que você se concentre no desenvolvimento rápido de aplicativos e na aceleração do tempo de colocação no mercado, em vez de gastar tempo e recursos preciosos para gerenciar máquinas virtuais e infraestrutura. Você pode continuar a desenvolver seu aplicativo com as ferramentas de software livre e a plataforma de sua escolha, sem precisar aprender novas habilidades.

A opção de implantação de servidor único oferece três tipos de preço: básico, Uso Geral e otimizado para memória. Cada escalão oferece capacidades de recursos diferentes para suportar as suas cargas de trabalho de base de dados. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Consulte [tipos de preço](concepts-pricing-tiers.md) para obter detalhes.

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Banco de dados do Azure para PostgreSQL-Citus (hiperescala)
A opção de hiperescala (Citus) dimensiona horizontalmente as consultas em vários computadores usando a fragmentação. Seu mecanismo de consulta paralelize consultas SQL de entrada entre esses servidores para obter respostas mais rápidas em grandes conjuntos de altos. Ele serve para aplicativos que exigem maior escala e desempenho, geralmente as cargas de trabalho que estão se aproximando-ou já excedem--100 GB de dados.

A opção de implantação de hiperescala (Citus) fornece:

- Dimensionamento horizontal em vários computadores usando a fragmentação
- A paralelização de consulta entre esses servidores para respostas mais rápidas em conjuntos de grandes volumes
- Excelente suporte para aplicativos multilocatários, análise operacional em tempo real e cargas de trabalho transacionais de alta taxa de transferência

Os aplicativos criados para PostgreSQL podem executar consultas distribuídas em Citus (hiperescala) com [bibliotecas de conexões](./concepts-connection-libraries.md) padrão e alterações mínimas.

## <a name="contacts"></a>Contactos
Para dúvidas ou sugestões sobre como trabalhar com o banco de dados do Azure para PostgreSQL, envie um email para a equipe do banco de dados do Azure para PostgreSQL ([@Ask BD do Azure para PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Esse endereço é para perguntas gerais em vez de tíquetes de suporte.

Além disso, considere estes pontos de contato conforme apropriado:
- Para entrar em contato com o suporte do Azure ou corrigir um problema com sua conta, [arquivo de um tíquete do portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Passos seguintes
- Consulte a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para ver as comparações de preços e as calculadoras.
- Comece criando seu primeiro banco de dados do Azure para PostgreSQL [Single Server](./quickstart-create-server-database-portal.md) ou [Citus (hiperescala)](./quickstart-create-hyperscale-portal.md)
- Criar a sua primeira aplicação em Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de ligação](./concepts-connection-libraries.md)

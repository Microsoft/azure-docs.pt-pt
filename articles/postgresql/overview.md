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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74481660"
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é a Base de Dados do Azure para PostgreSQL?
Azure Database for PostgreSQL é um serviço de base de dados relacional na nuvem da Microsoft construído para desenvolvedores. Baseia-se na versão comunitária do motor de base de dados [PostgreSQL](https://www.postgresql.org/) de código aberto, e está disponível em duas opções de implementação: Single Server e Hyperscale (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Base de Dados do Azure para PostgreSQL – Servidor Único
A opção de implementação do Servidor Único oferece:

- [Elevada disponibilidade](concepts-high-availability.md) incorporada sem custo adicional (99,99% SLA)
- Desempenho previsível com preços pay as you go inclusivos
- [Escala vertical conforme necessário](concepts-pricing-tiers.md) em segundos
- [Monitorização e alerta](concepts-monitoring.md) para avaliar o seu servidor
- Segurança e conformidade de nível empresarial
- [Protegido para proteger](concepts-security.md) dados sensíveis em repouso e em movimento
- [Backups automáticos e restauro](concepts-business-continuity.md) pontual por um período máximo de 35 dias


Todas estas funcionalidades não exigem praticamente qualquer ação de administração e são fornecidas sem custos adicionais. Permitem-lhe concentrar-se no rápido desenvolvimento de aplicações e acelerar o seu tempo para o mercado, em vez de gastar tempo e recursos preciosos para gerir máquinas virtuais e infraestruturas. Pode continuar a desenvolver a sua aplicação com as ferramentas e plataforma de código aberto à sua escolha, sem ter de aprender novas competências.

A opção de implementação do Single Server oferece três níveis de preços: Básico, Propósito Geral e Memória Otimizado. Cada escalão oferece capacidades de recursos diferentes para suportar as suas cargas de trabalho de base de dados. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Consulte os [níveis de preços](concepts-pricing-tiers.md) para obter mais detalhes.

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
A opção Hyperscale (Citus) escala horizontalmente consultas em várias máquinas usando sharding. O seu motor de consulta paralelamente as consultas SQL que chegam através destes servidores para respostas mais rápidas em grandes conjuntos de dados. Serve aplicações que requerem maior escala e desempenho, geralmente cargas de trabalho que se aproximam -- ou já excedem - 100 GB de dados.

A opção de implantação de Hiperescala (Citus) oferece:

- Escala horizontal através de várias máquinas usando sharding
- Paralelização de consultas através destes servidores para respostas mais rápidas em grandes conjuntos de dados
- Excelente apoio para aplicações multi-arrendatárias, análise operacional em tempo real e cargas de trabalho transacionais de alto produto

As aplicações construídas para postgreSQL podem executar consultas distribuídas em Hiperescala (Citus) com bibliotecas de [ligação](./concepts-connection-libraries.md) padrão e alterações mínimas.

## <a name="contacts"></a>Contactos
Para quaisquer perguntas ou sugestões sobre trabalhar com a Base de Dados Azure para PostgreSQL, envie um e-mail para a Base de Dados Azure para a Equipa PostgreSQL ([ @Ask Azure DB para PostgreSQL).](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) Este discurso destina-se a questões gerais e não a bilhetes de apoio.

Além disso, considere estes pontos de contacto conforme apropriado:
- Para contactar o Suporte Azure ou corrigir um problema com a sua conta, [preencha um bilhete no portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Passos seguintes
- Consulte a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para comparações de custos e calculadoras.
- Inicie a criação da sua primeira Base de Dados Azure para PostgreSQL [Single Server](./quickstart-create-server-database-portal.md) ou [Hyperscale (Citus)](./quickstart-create-hyperscale-portal.md)
- Criar a sua primeira aplicação em Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de ligação](./concepts-connection-libraries.md)

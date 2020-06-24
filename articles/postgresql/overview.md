---
title: Descrição geral do serviço de base de dados relacional da Base de Dados do Azure para PostgreSQL
description: Fornece uma descrição geral da Base de Dados do Azure para o serviço de base de dados relacional PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: deb720638e0921762eb9c5800d218c3cfccb7f55
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213606"
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é a Base de Dados do Azure para PostgreSQL?
Azure Database for PostgreSQL é um serviço de base de dados relacional na nuvem da Microsoft construída para desenvolvedores. Baseia-se na versão comunitária do motor de base de [dados PostgreSQL](https://www.postgresql.org/) de código aberto, e está disponível em duas opções de implementação: Single Server e Hyperscale (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Base de Dados do Azure para PostgreSQL – Servidor Único
A opção de implementação do Servidor Único fornece:

- Alta [disponibilidade](concepts-high-availability.md) incorporada sem custo adicional (99,99% SLA)
- Desempenho previsível com preços pay as you go inclusivos
- [Escala vertical, conforme necessário](concepts-pricing-tiers.md) em segundos
- [Monitorização e alerta](concepts-monitoring.md) para avaliar o seu servidor
- Segurança e conformidade de nível empresarial
- [Protegido para proteger dados](concepts-security.md) sensíveis em repouso e em movimento
- [Backups automáticos e restauro pontual](concepts-business-continuity.md) por um período máximo de 35 dias


Todas estas funcionalidades não exigem praticamente qualquer ação de administração e são fornecidas sem custos adicionais. Permitem-lhe focar-se no rápido desenvolvimento de aplicações e acelerar o seu tempo no mercado, em vez de gastar tempo e recursos preciosos para gerir máquinas virtuais e infraestruturas. Pode continuar a desenvolver a sua aplicação com as ferramentas de código aberto e plataforma à sua escolha, sem ter de aprender novas habilidades.

A opção de implementação do Servidor Único oferece três níveis de preços: Básico, Propósito Geral e Memória Otimizada. Cada escalão oferece capacidades de recursos diferentes para suportar as suas cargas de trabalho de base de dados. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Consulte [os níveis de preços](concepts-pricing-tiers.md) para mais detalhes.

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
A opção Hyperscale (Citus) escala horizontalmente as consultas em várias máquinas utilizando o fragmento. O seu motor de consulta paraleliza as consultas SQL recebidas nestes servidores para respostas mais rápidas em grandes conjuntos de dados. Serve aplicações que requerem maior escala e desempenho, geralmente cargas de trabalho que se aproximam -- ou já ultrapassam - 100 GB de dados.

A opção de implantação Hyperscale (Citus) oferece:

- Escala horizontal em várias máquinas usando o fragmento
- Paralelização de consultas através destes servidores para respostas mais rápidas em grandes conjuntos de dados
- Excelente suporte para aplicações multi-inquilinos, análise operacional em tempo real e elevadas cargas de trabalho transacionais de produção

As aplicações construídas para PostgreSQL podem executar consultas distribuídas em Hiperescala (Citus) com bibliotecas de [conexão](./concepts-connection-libraries.md) padrão e alterações mínimas.

## <a name="contacts"></a>Contactos
Para quaisquer perguntas ou sugestões sobre trabalhar com a Azure Database for PostgreSQL, envie um e-mail para a Base de Dados Azure para a Equipa PostgreSQL[ @Ask (Azure DB para PostgreSQL).](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) Este endereço destina-se a questões gerais e não a bilhetes de apoio.

Além disso, considere estes pontos de contacto adequados:
- Para contactar o Suporte Azure ou corrigir um problema com a sua conta, [arquive um bilhete a partir do portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Passos seguintes
- Consulte [a página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para comparar custos e calculadoras. Tanto as opções de implementação single Server e Hyperscale (Citus) oferecem descontos de instância reservado pré-pagos, consulte os [preços de Single Server RI](concept-reserved-pricing.md) e as páginas de preços de [Hyperscale RI](concepts-hyperscale-reserved-pricing.md) para obter detalhes.
- Começa por criar a sua primeira Base de Dados Azure para [Servidor Único](./quickstart-create-server-database-portal.md) PostgreSQL ou [Hiperescala (Citus)](./quickstart-create-hyperscale-portal.md)
- Criar a sua primeira aplicação em Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de ligação](./concepts-connection-libraries.md)

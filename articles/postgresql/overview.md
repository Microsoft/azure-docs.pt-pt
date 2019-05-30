---
title: Descrição geral do serviço de base de dados relacional da Base de Dados do Azure para PostgreSQL
description: Fornece uma descrição geral da Base de Dados do Azure para o serviço de base de dados relacional PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: f4023fa84215a0319669de0d812d8306b62278e3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65073275"
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é a Base de Dados do Azure para PostgreSQL?
Base de dados do Azure para PostgreSQL é um serviço de base de dados relacional na cloud da Microsoft criado para programadores. Baseia-se na versão de Comunidade de open source [PostgreSQL](https://www.postgresql.org/) motor de base de dados e está disponível em duas opções de implementação: Servidor único e a Hiperescala (Citus) (pré-visualização).

## <a name="azure-database-for-postgresql---single-server"></a>Base de dados do Azure para PostgreSQL - servidor único
Oferece a opção de implementação de servidor único:

- Elevada disponibilidade incorporada sem custos adicionais (SLA de 99,99%)
- Desempenho previsível com preços pay as you go inclusivos
- Dimensionamento vertical, conforme necessário em segundos
- Monitorização e alertas para avaliar rapidamente o impacto de dimensionamento
- Segurança para proteger os dados confidenciais inativos e em movimento
- Cópias de segurança automáticas e restauro para um ponto anterior no tempo até 35 dias
- Segurança e conformidade de nível empresarial

Todas estas funcionalidades não exigem praticamente qualquer ação de administração e são fornecidas sem custos adicionais. Eles permitem que se concentre em desenvolvimento rápido de aplicativos e acelerar o seu tempo de comercialização, em vez de gastar tempo precioso e recursos para gerir máquinas virtuais e infraestruturas. Pode continuar a desenvolver a sua aplicação com as ferramentas de código-fonte aberto e a plataforma à sua escolha, sem ter de aprender novas competências.

A opção de implementação de servidor único oferece três escalões de preço: Básico, fins gerais e com otimização de memória. Cada escalão oferece capacidades de recursos diferentes para suportar as suas cargas de trabalho de base de dados. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Ver [escalões de preço](concepts-pricing-tiers.md) para obter detalhes.

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização)
A opção de Hiperescala (Citus) aumenta horizontalmente consultas em várias máquinas com a fragmentação. Seu mecanismo de consulta processa a entrada consultas SQL por estes servidores para respostas mais rápidas em grandes conjuntos de dados. Ele serve aplicações que necessitam de maior dimensionamento e desempenho, em geral, cargas de trabalho que estão prestes a atingir – ou já excederem – 100 GB de dados.

Oferece a opção de implementação de Hiperescala (Citus):

- Horizontal dimensionamento em várias máquinas com fragmentação
- Paralelização de consultas em todos esses servidores para respostas mais rápidas em grandes conjuntos de dados
- Suporte excelente para aplicações multi-inquilino, análise operacional em tempo real e cargas de trabalho transacionais de alto débito

Os aplicativos criados para o PostgreSQL pode executar consultas distribuídas em grande escala (Citus) com o padrão [bibliotecas de ligação](./concepts-connection-libraries.md) e alterações mínimas.

Tenha em atenção que a Hiperescala (Citus) está em pré-visualização pública e, assim não ainda oferece um SLA.

## <a name="data-security"></a>Segurança de dados
Base de dados do Azure para PostgreSQL mantém tradição dos serviços de base de dados do Azure de segurança de dados. Inclui funcionalidades que limitam o acesso, proteger os dados inativos e em movimento e ajudar a monitorizar a atividade. Visite o [Centro de Fidedignidade do Azure](https://azure.microsoft.com/overview/trusted-cloud/) para obter informações sobre a segurança da plataforma do Azure.

A base de dados do Azure para o serviço PostgreSQL utiliza a encriptação de armazenamento para dados inativos e está em conformidade com a FIPS 140-2. Dados, incluindo cópias de segurança, são encriptados em disco. O serviço utiliza a cifra AES de 256 bits incluída na encriptação de armazenamento do Azure e as chaves são geridas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada. Por predefinição, a base de dados do Azure para o serviço PostgreSQL requer ligações seguras para dados em movimento tanto através da rede entre o aplicativo de banco de dados e de cliente.

## <a name="contacts"></a>Contactos
Para quaisquer perguntas ou sugestões sobre como trabalhar com a base de dados do Azure para PostgreSQL, envie um e-mail para a base de dados do Azure para PostgreSQL Team ([ @Ask DB do Azure para PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Este endereço é para perguntas gerais em vez de pedidos de suporte.

Além disso, considere estes pontos de contacto conforme apropriado:
- Contacte o suporte do Azure ou corrigir um problema com a sua conta [enviar um pedido do portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Passos Seguintes
- Consulte a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para ver as comparações de preços e as calculadoras.
- Comece por criar a sua primeira base de dados do Azure para PostgreSQL [servidor único](./quickstart-create-server-database-portal.md) ou [Hiperescala (Citus) (pré-visualização)](./quickstart-create-hyperscale-portal.md)
- Crie seu primeiro aplicativo em Python, PHP, Ruby, C\#, Java, node. js: [Bibliotecas de ligação](./concepts-connection-libraries.md)

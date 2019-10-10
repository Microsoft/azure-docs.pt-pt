---
title: Descrição geral do serviço de base de dados relacional da Base de Dados do Azure para PostgreSQL
description: Fornece uma descrição geral da Base de Dados do Azure para o serviço de base de dados relacional PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: 5194cf51fd7f1debeba76edb48e8377919ae448a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177957"
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é a Base de Dados do Azure para PostgreSQL?
O banco de dados do Azure para PostgreSQL é um serviço de banco de dados relacional no Microsoft Cloud criado para desenvolvedores. Ele se baseia na versão da Comunidade do mecanismo de banco de dados [PostgreSQL](https://www.postgresql.org/) de software livre e está disponível em duas opções de implantação: servidor único e hiperescala (Citus) (visualização).

## <a name="azure-database-for-postgresql---single-server"></a>Banco de dados do Azure para PostgreSQL-servidor único
A opção de implantação de servidor único fornece:

- Alta disponibilidade interna sem custo adicional (99,99% de SLA)
- Desempenho previsível com preços pay as you go inclusivos
- Escala vertical conforme necessário em segundos
- Monitoramento e alertas para avaliar rapidamente o impacto do dimensionamento
- Segurança para proteger os dados confidenciais inativos e em movimento
- Cópias de segurança automáticas e restauro para um ponto anterior no tempo até 35 dias
- Segurança e conformidade de nível empresarial

Todas estas funcionalidades não exigem praticamente qualquer ação de administração e são fornecidas sem custos adicionais. Eles permitem que você se concentre no desenvolvimento rápido de aplicativos e na aceleração do tempo de colocação no mercado, em vez de gastar tempo e recursos preciosos para gerenciar máquinas virtuais e infraestrutura. Você pode continuar a desenvolver seu aplicativo com as ferramentas de software livre e a plataforma de sua escolha, sem precisar aprender novas habilidades.

A opção de implantação de servidor único oferece três tipos de preço: básico, Uso Geral e otimizado para memória. Cada escalão oferece capacidades de recursos diferentes para suportar as suas cargas de trabalho de base de dados. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Consulte [tipos de preço](concepts-pricing-tiers.md) para obter detalhes.

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Banco de dados do Azure para PostgreSQL-hiperescala (Citus) (visualização)
A opção de hiperescala (Citus) dimensiona horizontalmente as consultas em vários computadores usando a fragmentação. Seu mecanismo de consulta paralelize consultas SQL de entrada entre esses servidores para obter respostas mais rápidas em grandes conjuntos de altos. Ele serve para aplicativos que exigem maior escala e desempenho, geralmente as cargas de trabalho que estão se aproximando-ou já excedem--100 GB de dados.

A opção de implantação de hiperescala (Citus) fornece:

- Dimensionamento horizontal em vários computadores usando a fragmentação
- A paralelização de consulta entre esses servidores para respostas mais rápidas em conjuntos de grandes volumes
- Excelente suporte para aplicativos multilocatários, análise operacional em tempo real e cargas de trabalho transacionais de alta taxa de transferência

Os aplicativos criados para PostgreSQL podem executar consultas distribuídas em Citus (hiperescala) com [bibliotecas de conexões](./concepts-connection-libraries.md) padrão e alterações mínimas.

Observe que o Citus (hiperescala) está em visualização pública e, como tal, ainda não oferece um SLA.

## <a name="data-security"></a>Segurança dos dados
O banco de dados do Azure para PostgreSQL mantém a tradição da segurança dos serviços do Azure Database Services. Ele tem recursos que limitam o acesso, protegem dados em repouso e em movimento e ajudam a monitorar a atividade. Visite o [Centro de Fidedignidade do Azure](https://azure.microsoft.com/overview/trusted-cloud/) para obter informações sobre a segurança da plataforma do Azure.

O serviço banco de dados do Azure para PostgreSQL usa o módulo de criptografia FIPS 140-2 validado para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco com a exceção de arquivos temporários criados durante a execução de consultas. O serviço usa a codificação AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada. Por padrão, o serviço banco de dados do Azure para PostgreSQL requer conexões seguras para dados em movimento em toda a rede e entre o banco de dados e o aplicativo cliente.

## <a name="contacts"></a>Contactos
Para dúvidas ou sugestões sobre como trabalhar com o banco de dados do Azure para PostgreSQL, envie um email para a equipe do banco de dados do Azure para PostgreSQL ([@Ask BD do Azure para PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Esse endereço é para perguntas gerais em vez de tíquetes de suporte.

Além disso, considere estes pontos de contato conforme apropriado:
- Para entrar em contato com o suporte do Azure ou corrigir um problema com sua conta, [arquivo de um tíquete do portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Passos seguintes
- Consulte a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para ver as comparações de preços e as calculadoras.
- Comece criando seu primeiro banco de dados do Azure para PostgreSQL [Single Server](./quickstart-create-server-database-portal.md) ou [Citus (visualização)](./quickstart-create-hyperscale-portal.md)
- Criar a sua primeira aplicação em Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de ligação](./concepts-connection-libraries.md)

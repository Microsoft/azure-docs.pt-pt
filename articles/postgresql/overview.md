---
title: O que é Azure Database para PostgreSQL
description: Fornece uma visão geral da Base de Dados Azure para o serviço de base de dados relacional PostgreSQL no contexto do servidor flexível.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: c3ea7930f41fe89538a817da032e993e534db9cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92491330"
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é a Base de Dados do Azure para PostgreSQL?

Azure Database for PostgreSQL é um serviço de base de dados relacional na nuvem da Microsoft com base no motor [de base de dados PostgreSQL Community Edition](https://www.postgresql.org/) (disponível sob a licença GPLv2). A Base de Dados do Azure para PostgreSQL oferece:

- Elevada disponibilidade incorporada.
- Proteção de dados utilizando cópias de segurança automáticas e restauro pontual por um período máximo de 35 dias.
- Manutenção automatizada para hardware subjacente, sistema operativo e motor de base de dados para manter o serviço seguro e atualizado.
- Desempenho previsível, com preços pay as you go inclusivos.
- Escalagem elástica em segundos.
- Segurança de nível empresarial e conformidade líder do setor para proteger dados sensíveis em repouso e em movimento.
- Monitorização e automatização para simplificar a gestão e monitorização para implementações em larga escala.
- Experiência de apoio líder na indústria.

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Base de Dados do Azure para PostgreSQL":::

Estas funcionalidades não exigem praticamente qualquer ação de administração e são todas fornecidas sem custos adicionais. Permitem-lhe focar-se no rápido desenvolvimento de aplicações e acelerar o seu tempo no mercado em vez de alocar tempo e recursos preciosos à gestão de máquinas e infraestruturas virtuais. Além disso, pode continuar a desenvolver a sua aplicação, com a plataforma e as ferramentas open-source da sua preferência, e garantir a velocidade e a eficiência exigidas pelo seu negócio sem ter de adquirir novas competências.

## <a name="deployment-models"></a>Modelos de implementação

A azure Database for PostgreSQL powered by the PostgreSQL community edition está disponível em três modos de implementação:

- Servidor Único
- Servidor Flexível (Pré-visualização)
- Hyperscale (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Base de Dados do Azure para PostgreSQL – Servidor Único

A azure Database for PostgreSQL Single Server é um serviço de base de dados totalmente gerido com requisitos mínimos para personalizaçãos de bases de dados. A plataforma de servidor único foi concebida para lidar com a maioria das funções de gestão da base de dados, tais como patching, backups, alta disponibilidade, segurança com a configuração e controlo mínimos do utilizador. A arquitetura está otimizada para alta disponibilidade incorporada com 99,99% de disponibilidade em zona de disponibilidade única. Suporta a versão comunitária de PostgreSQL 9.5, 9,6, 10 e 11. O serviço está geralmente disponível hoje em dia em grande variedade de regiões de [Azure.](https://azure.microsoft.com/global-infrastructure/services/)

A opção de implementação Servidor Único oferece três escalões de preço: Básico, Fins Geral e Otimizada para Memória. Cada escalão oferece capacidades de recursos diferentes para suportar as suas cargas de trabalho de base de dados. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, dimensionar para satisfazer as necessidades da sua solução. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Apenas paga pelos recursos necessário e apenas quando que precisar dos mesmos. Consulte [Escalões de preço](./concepts-pricing-tiers.md) para obter mais detalhes.

Os servidores individuais são mais adequados para aplicações nativas em nuvem concebidas para lidar com remendos automatizados sem a necessidade de controlo granular na programação de remendos e configurações de configuração postgreSQL personalizadas.

Para uma visão geral detalhada do modo de implementação de um único servidor, consulte [uma visão geral do servidor único](./overview-single-server.md).

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>Base de Dados do Azure para PostgreSQL - Servidor Flexível (Pré-visualização)

A Azure Database for PostgreSQL Flexible Server é um serviço de base de dados totalmente gerido projetado para fornecer mais controlo granular e flexibilidade sobre funções de gestão de bases de dados e configurações de configuração. Em geral, o serviço proporciona mais flexibilidade e personalizaçãos com base nos requisitos do utilizador. A arquitetura flexível do servidor permite que os utilizadores optem pela alta disponibilidade dentro de uma única zona de disponibilidade e em várias zonas de disponibilidade. O Flexible Server fornece controlos de otimização de custos melhores com a capacidade de parar/iniciar o servidor e o nível de computação rebentado, ideal para cargas de trabalho que não necessitam de capacidade computacional completa continuamente. O serviço suporta atualmente a versão comunitária do PostgreSQL 11 e 12 com planos para adicionar versões mais recentes em breve. O serviço encontra-se atualmente em pré-visualização pública, disponível hoje em grande variedade de regiões de Azure.

Servidores flexíveis são os mais adequados para

- Desenvolvimentos de aplicações que requerem melhor controlo e personalização.
- Controlos de otimização de custos com a capacidade de parar/iniciar o servidor.
- Zona redundante alta disponibilidade
- Janelas de manutenção geridas
  
Para obter uma visão geral detalhada do modo de implementação flexível do servidor, consulte [a visão geral do servidor flexível](./flexible-server/overview.md).

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

A opção Hyperscale (Citus) dimensiona horizontalmente as consultas em múltiplos computadores através da fragmentação. O seu motor de consulta paraleliza consultas SQL de entrada nestes servidores para obter respostas mais rápidas em grandes conjuntos de dados. É adequado a aplicações que exigem um maior dimensionamento e desempenho, cargas de trabalho que geralmente se aproximam ou já excederam 100 GB de dados.

A opção de implementação Hyperscale (Citus) fornece:

- Dimensionamento horizontal em múltiplos computadores através da fragmentação
- Paralelização de consultas nestes servidores para obter respostas mais rápidas em grandes conjuntos de dados
- Excelente suporte para aplicações de múltiplos inquilinos, análise operacional em tempo real e elevadas cargas de trabalho de débito transacional
  
As aplicações construídas para PostgreSQL podem executar consultas distribuídas em Hiperescala (Citus) com bibliotecas de [conexão](./concepts-connection-libraries.md) padrão e alterações mínimas.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os três modos de implementação da Base de Dados Azure para PostgreSQL e escolha as opções certas com base nas suas necessidades.

- [Servidor Único](./overview-single-server.md)
- [Servidor Flexível](./flexible-server/overview.md)
- Hyperscale (Citus)
---
title: Armazenar, gerir e persistir dados de aplicação na nuvem com visual studio app center e serviços Azure
description: Conheça serviços como o Visual Studio App Center que lhe permitem armazenar, gerir e persistir dados de aplicações móveis na nuvem.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: cb76151a3f952f932cc533b771283b5d45382366
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873454"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Armazenar, sincronizar e consultar dados de aplicações móveis da nuvem
Independentemente do tipo de aplicação que constrói, provavelmente gerará e processará dados. Os utilizadores da sua aplicação têm altas expectativas. Querem que a aplicação funcione de forma rápida e perfeita, em todas as circunstâncias. A maioria das aplicações também funcionam em vários dispositivos. Pode aceder à sua aplicação a partir de um ambiente de trabalho ou de um dispositivo móvel. Vários utilizadores podem utilizar a aplicação ao mesmo tempo e partilhar dados com a expectativa de obter acesso instantâneo e em tempo real aos dados.

Os utilizadores da sua aplicação nem sempre terão conectividade com a Internet. As aplicações são projetadas e espera-se que funcionem com ou sem ligação à Internet. Os desenvolvedores devem escolher a solução certa para armazenar e sincronizar os seus dados na nuvem para proporcionar uma grande experiência do cliente para a sua aplicação.

A Microsoft fornece uma variedade de serviços que eliminam a necessidade de girar servidores, escolher a sua base de dados ou preocupar-se com escala ou segurança para fornecer a experiência mais rica possível. Estes serviços proporcionam uma excelente experiência de desenvolvimento que permite armazenar dados de aplicações na nuvem utilizando APIs SQL ou NoSQL. Também pode sincronizar dados em todos os dispositivos e permitir que a aplicação funcione com ou sem uma ligação de rede para ajudar a construir aplicações escaláveis e robustas.

Utilize os seguintes serviços para gerir e armazenar dados de aplicações móveis na nuvem.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[O Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é um serviço de base de dados multimodelo distribuído globalmente. Podeusá-lo para construir aplicações à escala do planeta. Com o Azure Cosmos DB, você pode escalar earmazenamento ereto de escala elástica e independente em várias regiões azure em todo o mundo. Pode aproveitar o acesso rápido de dados de um dígito, utilizando as suas superfícies API favoritas. Estas superfícies incluem SQL, MongoDB, Cassandra, Tabelas ou Gremlin. A Azure Cosmos DB fornece exclusivamente acordos abrangentes de nível de serviço (SLAs) para a entrada, latência, disponibilidade e consistência.

**Principais funcionalidades**
- Suporta uma vasta gama de APIs, que inclui a API SQL (Core), Cassandra API, MongoDB API, Gremlin API e Table API.
- A distribuição global chave na mão replica os seus dados onde quer que os seus utilizadores estejam. Os seus utilizadores podem interagir com uma réplica dos dados que lhes é mais próximo.
- Nenhum esquema ou gestão de índices porque o motor de base de dados é totalmente agnóstico.
- Presença regional ubíqua porque o Azure Cosmos DB está disponível em todas as regiões do Azure em todo o mundo, que inclui mais de 54 regiões na nuvem pública.
- Escolhas de consistência múltipla precisamente definidas porque o protocolo de replicação multi-master da Azure Cosmos DB é cuidadosamente projetado para oferecer cinco opções de consistência bem definidas. Estas cinco escolhas são fortes, delimitados, sessão, prefixo consistente, e eventualmente.
- 99,999% disponibilidade para leituras e escritos.
- Programática (ou através do portal Azure) invoca minávia regional da sua conta Azure Cosmos DB para garantir que a sua aplicação foi concebida para resistir a um desastre regional.
- Latista garantida no percentil 99 em todo o mundo.

**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Documentação azure Cosmos DB](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure
 [A Base de Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) é um serviço gerido por base de dados relacional de uso geral. Pode usá-lo para criar uma camada de armazenamento de dados altamente disponível e de alto desempenho para aplicações e soluções na nuvem Azure.

**Principais funcionalidades**
- **Modelos e ferramentas de base de dados elásticas:** Com uma base de dados elástica, os desenvolvedores podem juntar recursos entre um grupo de bases de dados para escalar. Para gerir administrativamente estes recursos, submete-se um guião como trabalho. A base de dados SQL executa então o script através das bases de dados.
- **Alto desempenho:** Aplicações de alta-adesão podem tirar partido da versão mais recente. Fornece mais 25% de potência de base de dados premium.
- **Backups, replicação e alta disponibilidade:** A replicação incorporada e um SLA apoiado pela Microsoft a nível da base de dados fornecem continuidade e proteção de aplicações contra eventos catastróficos. A geo-replicação ativa permite configurar a falha e a restauração do self-service, que fornecem o controlo total sobre a "recuperação de oops". A restauração de dados está disponível a partir de cópias de segurança de dados até 35 dias.
- **Manutenção quase nula:** O software automático faz parte do serviço. As réplicas do sistema incorporado ajudam a fornecer proteção de dados inerente, tempo de uptime de base de dados e estabilidade do sistema. As réplicas do sistema são automaticamente transferidas para novos computadores. São provisionados à medida que os antigos falham.
- **Segurança:** A Azure SQL Database oferece um portfólio de funcionalidades de segurança para atender às políticas de conformidade mandatadas pela organização ou pela indústria:
    - A auditoria proporciona aos desenvolvedores a capacidade de executar tarefas relacionadas com a conformidade e de adquirir conhecimento sobre atividades.
    - Os desenvolvedores e tipodem implementar políticas ao nível da base de dados para ajudar a limitar o acesso a dados sensíveis com segurança ao nível da linha, máscara de dados dinâmicos e encriptação transparente de dados para a Base de Dados Azure SQL.
    - A Base de Dados Azure SQL é verificada pelos principais auditores em nuvem como parte do âmbito das principais certificações e aprovações de conformidade azure, tais como HIPAA BAA, ISO/IEC 27001:2005, FedRAMP e Cláusulas de Modelo da UE.

**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Documentação da Base de Dados SQL do Azure](/azure/sql-database/) 

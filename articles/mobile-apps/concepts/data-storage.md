---
title: Armazenar, gerir e persistir dados de aplicações na nuvem com o Visual Studio App Center e os serviços Azure
description: Conheça os serviços como o Visual Studio App Center que lhe permitem armazenar, gerir e persistir dados de aplicações móveis na nuvem.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 724ad41bdafdba976939cbf9b7f88df2de6c6c18
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450669"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Armazenar, sincronizar e consultar dados de aplicações móveis da nuvem
Não importa que tipo de aplicação construa, provavelmente gerará e processará dados. Os utilizadores da sua aplicação têm altas expectativas. Querem que a candidatura funcione de forma rápida e perfeita, em todas as circunstâncias. A maioria das aplicações também funciona em vários dispositivos. Pode aceder à sua aplicação a partir de um ambiente de trabalho ou de um dispositivo móvel. Vários utilizadores poderão utilizar a aplicação ao mesmo tempo e partilhar dados com a expectativa de obter acesso instantâneo e em tempo real aos dados.

Os utilizadores da sua aplicação nem sempre terão conectividade com a Internet. As aplicações são projetadas e espera-se que funcionem com ou sem ligação à Internet. Os desenvolvedores devem escolher a solução certa para armazenar e sincronizar os seus dados na nuvem para proporcionar uma grande experiência ao cliente para a sua aplicação.

A Microsoft fornece uma variedade de serviços que eliminam a necessidade de girar servidores, escolher a sua base de dados ou preocupar-se com a escala ou segurança para fornecer a experiência o mais rica possível. Estes serviços proporcionam uma grande experiência de desenvolvedor que permite armazenar dados de aplicações na nuvem usando APIs SQL ou NoSQL. Também é possível sincronizar dados em todos os dispositivos e permitir que a aplicação funcione com ou sem ligação de rede para ajudar a construir aplicações escaláveis e robustas.

Utilize os seguintes serviços para gerir e armazenar dados de aplicações móveis na nuvem.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é um serviço de base de dados multi-modelo distribuído globalmente. Podes usá-lo para construir aplicações à escala planetário. Com a Azure Cosmos DB, você pode escalar elasticamente e independentemente a produção e armazenamento em qualquer número de regiões de Azure em todo o mundo. Você pode aproveitar o acesso rápido de dados de um dígito de um dígito, usando as suas superfícies API favoritas. Estas superfícies incluem SQL, MongoDB, Cassandra, Tables ou Gremlin. A Azure Cosmos DB oferece exclusivamente acordos abrangentes de nível de serviço (SLAs) para produção, latência, disponibilidade e consistência.

**Principais funcionalidades**
- Suporta uma vasta gama de APIs, que inclui a API SQL (Core), Cassandra API, MongoDB API, Gremlin API e Table API.
- A distribuição global chave na mão replica os seus dados onde quer que os seus utilizadores estejam. Os seus utilizadores podem interagir com uma réplica dos dados mais próximos dos mesmos.
- Sem schema ou gestão de índices porque o motor da base de dados é totalmente agnóstico.
- Presença regional ubíqua porque Azure Cosmos DB está disponível em todas as regiões do Azure em todo o mundo, que inclui mais de 54 regiões na nuvem pública.
- Precisamente definido, múltiplas escolhas de consistência porque o protocolo de replicação multi-mestre da Azure Cosmos DB é cuidadosamente concebido para oferecer cinco escolhas de consistência bem definidas. Estas cinco escolhas são fortes, limitadas, estagnadas, sessão, prefixo consistente, e eventual.
- 99,999% de disponibilidade para leituras e escritas.
- Programáticamente (ou através do portal Azure) invoca o fracasso regional da sua conta DB Azure Cosmos para garantir que a sua aplicação foi concebida para resistir a um desastre regional.
- Latência baixa garantida no percentil 99 em todo o mundo.

**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Documentação DB de Azure Cosmos](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure
 [A Azure SQL Database](https://azure.microsoft.com/services/sql-database/) é um serviço de base de dados relacional de uso geral gerido. Pode usá-lo para criar uma camada de armazenamento de dados altamente disponível e de alto desempenho para aplicações e soluções na nuvem Azure.

**Principais funcionalidades**
- **Modelos e ferramentas de base de dados elásticas:** Com uma base de dados elástica, os desenvolvedores podem reunir recursos entre um grupo de bases de dados para dimensionamento. Para gerir administrativamente estes recursos, submete-se a um guião como trabalho. A base de dados SQL executa então o script através das bases de dados.
- **Alto desempenho:** As aplicações de alto rendimento podem tirar partido da versão mais recente. Fornece 25% mais poder de base de dados premium.
- **Backups, replicação e alta disponibilidade:** A replicação incorporada e um SLA apoiado pela Microsoft ao nível da base de dados fornecem continuidade e proteção da aplicação contra eventos catastróficos. A geo-replicação ativa permite configurar o failover e o autosserviço restaurado, que proporcionam total controlo sobre a "recuperação de oops". A restauração de dados está disponível a partir de cópias de dados até 35 dias.
- **Manutenção quase nula:** O software automático faz parte do serviço. As réplicas incorporadas do sistema ajudam a fornecer proteção de dados inerente, tempo de base de dados e estabilidade do sistema. As réplicas do sistema são automaticamente transferidas para novos computadores. Estão a provisionados à medida que os antigos falham.
- **Segurança:** A Azure SQL Database oferece um portfólio de funcionalidades de segurança para atender às políticas de conformidade organizacionais ou mandatais pela indústria:
    - A auditoria proporciona aos desenvolvedores a capacidade de executar tarefas relacionadas com o cumprimento e de adquirir conhecimento sobre atividades.
    - Os desenvolvedores e TI podem implementar políticas ao nível da base de dados para ajudar a limitar o acesso a dados sensíveis com segurança ao nível da linha, máscara de dados dinâmica e encriptação de dados transparente para a Base de Dados Azure SQL.
    - A base de dados Azure SQL é verificada pelos principais auditores em nuvem como parte do âmbito das principais certificações e aprovações de conformidade da Azure, tais como HIPAA BAA, ISO/IEC 27001:2005, FedRAMP e Cláusulas-Modelo da UE.

**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Documentação da Base de Dados SQL do Azure](/azure/sql-database/) 

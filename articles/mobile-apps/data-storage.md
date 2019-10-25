---
title: Armazene, gerencie e persista dados de aplicativos na nuvem com Visual Studio App Center e serviços do Azure
description: Saiba mais sobre os serviços como App Center que permitem armazenar, gerenciar e persistir dados de aplicativos móveis na nuvem.
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b7043c7e9bd944bfb3633397edfa3c35609bd8ec
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795578"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Armazene, sincronize e consulte dados de aplicativos móveis da nuvem
Não importa o tipo de aplicativo que você está criando, você provavelmente estará gerando e processando dados. As expectativas do usuário são altas e querem que o aplicativo funcione de forma rápida e direta, em todas as circunstâncias. A maioria dos aplicativos funciona em vários dispositivos, portanto, quando você acessa seu aplicativo, você pode acessá-lo de um dispositivo móvel ou desktop. Vários usuários podem estar usando o aplicativo ao mesmo tempo e compartilhando dados com a expectativa de obter acesso imediato e em tempo real aos dados.

Além disso, os usuários do aplicativo nem sempre terão conectividade com a Internet. os aplicativos são projetados e devem estar trabalhando com ou sem conexão com a Internet. Com todas essas complexidades crescentes, é uma tarefa enorme para os desenvolvedores escolherem a solução certa para armazenar e sincronizar seus dados na nuvem para fornecer uma excelente experiência do cliente para seu aplicativo.

A Microsoft fornece uma variedade de serviços que eliminam a necessidade de criar servidores, escolher seu banco de dados ou se preocupar com escala ou segurança para fornecer a experiência mais rica possível. Esses serviços fornecem uma excelente experiência de desenvolvedor que permite armazenar dados de aplicativos na nuvem usando APIs SQL ou NoSQL, sincronizar dados em todos os dispositivos e permitir que o aplicativo funcione com ou sem uma conexão de rede para ajudar a criar aplicativos escalonáveis e robustos aplicativos.

Use os seguintes serviços para gerenciar e armazenar dados de aplicativos móveis na nuvem.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center dados](/appcenter/data/) é um serviço de gerenciamento de dados que permite que os aplicativos gerenciem, persistam e sincronizem dados de aplicativos na nuvem em diferentes dispositivos e plataformas em cenários online e offline. **Criado com base no serviço de Cosmos DB** que é dimensionado conforme a medida de seu usuário e o número de aplicativos aumenta, esse serviço garante baixa latência, alta disponibilidade e alta escalabilidade para todos os seus dados.

**Principais recursos**
- **Provisione facilmente um novo banco de dados Cosmos DB** ou **Conecte-se a um banco de dados Cosmos DB existente** no portal do App Center.
- O **suporte a banco** de dados NoSQL para armazenar, sincronizar e consultar facilmente o aplicativo.
- **Criado com base em Cosmos DB**, esse serviço herda a maioria dos principais recursos oferecidos pelo Azure Cosmos DB e pode ser **dimensionado globalmente** para atender às suas necessidades de negócios.
- Recursos de **sincronização online e offline** para sincronizar dados entre dispositivos.
- **SDKs de cliente** móvel que permitem que você gerencie facilmente os dados de aplicativos privados.
- **Suporte à plataforma** -Ios, Android, Xamarin, reagir nativo.

**Referências**
- [Inscreva-se com App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introdução aos dados de App Center](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft que ajuda a criar aplicativos em escala de planeta. Com um clique de um botão, o Cosmos DB permite dimensionar de forma elástica e independente a taxa de transferência e o armazenamento em qualquer número de regiões do Azure em todo o mundo. Você pode dimensionar de forma elástica a taxa de transferência e o armazenamento e aproveitar o acesso a dados rápido e com um único dígito de milissegundos usando suas superfícies de API favoritas, incluindo SQL, MongoDB, Cassandra, Tables ou Gremlin. Cosmos DB fornece exclusivamente SLAs (contratos de nível de serviço) para taxa de transferência, latência, disponibilidade e consistência.

**Principais recursos**
- O oferece suporte a uma **ampla gama de APIs** inclui a API do SQL (núcleo), API do Cassandra, API do MONGODB, API Gremlin e API de tabela.
- A **distribuição global completa** Replica seus dados onde quer que os usuários estejam, para que os usuários possam interagir com uma réplica dos dados mais próximos deles.
- **Nenhum gerenciamento de esquema ou de índice** como o mecanismo de banco de dados é totalmente independente de esquema.
- A **presença regional onipresente** como Cosmos DB está disponível em todas as regiões do Azure em todo o mundo, incluindo mais de 54 regiões na nuvem pública.
- **Precisamente definidas, várias opções de consistência,** como o protocolo de replicação de vários mestres de Cosmos DB, são cuidadosamente projetadas para oferecer cinco opções de consistência bem definidas – forte, desatualização limitada, sessão, prefixo consistente e eventual.
- **99,999% de disponibilidade** para leituras e gravações.
- **Programaticamente (ou via portal) invocam o failover regional** da sua conta do cosmos para garantir que seu aplicativo seja projetado para resistir a um desastre regional.
- **Baixa latência garantida em 99 º percentil, em**todo o mundo.

**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Sobre](/azure/cosmos-db/introduction)   

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure
 O banco de dados [SQL do Azure](https://azure.microsoft.com/services/sql-database/) é um serviço gerenciado de banco de dados relacional de uso geral que permite que você crie uma camada de armazenamento altamente disponível e de alto desempenho para aplicativos e soluções na nuvem do Azure.

**Principais recursos**
- **Ferramentas e modelos de banco de dados elástico** – um banco de dados elástico oferece aos desenvolvedores a capacidade de agrupar recursos para usar entre um grupo de bancos de dados para dimensionamento, que pode ser administrado administrativamente enviando um script como um trabalho, e o banco de dados SQL executa o script entre os bancos de dados.
- **Alto desempenho** -aplicativos de alta taxa de transferência podem aproveitar a versão mais recente, que oferece 25% mais potência de banco de dados Premium.
- **Backups, replicação e alta disponibilidade** – replicação interna e um SLA com suporte da Microsoft no nível do banco de dados fornecem continuidade de aplicativos e proteção contra eventos catastróficos. A replicação geográfica ativa permite que você configure o failover e a restauração de autoatendimento, que fornecem controle total sobre "recuperação de ops" (restauração de dados de backups de dados disponíveis de até 35 dias).
- **Manutenção quase zero** – o software automático faz parte do serviço, e as réplicas internas do sistema ajudam a fornecer proteção de dados inerente, tempo de atividade do banco de dados e estabilidade do sistema. As réplicas do sistema são movidas automaticamente para computadores novos que são rapidamente aprovisionados à medida que os computadores antigos se vão avariando.
- **Segurança** -o banco de dados SQL oferece um portfólio de recursos de segurança para atender às políticas de conformidade da organização ou das normas do setor
    - A auditoria fornece aos desenvolvedores a capacidade de executar tarefas relacionadas à conformidade e obter conhecimento sobre atividades.
    - Os desenvolvedores e ti podem implementar políticas no nível de banco de dados para ajudar a limitar o acesso a dados confidenciais com segurança em nível de linha, mascaramento de dados dinâmicos e Transparent Data Encryption para o banco de dados SQL do Azure.
    - O banco de dados SQL é verificado pelos principais auditores de nuvem como parte do escopo das principais certificações e aprovações de conformidade do Azure, como HIPAA BAA, ISO/IEC 27001:2005, FedRAMP e cláusulas de modelo da UE).

**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Sobre](/azure/sql-database/)
   
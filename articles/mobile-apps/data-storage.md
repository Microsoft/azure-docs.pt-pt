---
title: Armazene, gerencie e persista dados de aplicativos na nuvem com Visual Studio App Center e serviços do Azure
description: Saiba mais sobre serviços como Visual Studio App Center que permitem armazenar, gerenciar e persistir dados de aplicativos móveis na nuvem.
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 04de01e1fd3dba88145c7f3748ca30b31ab1740a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454452"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Armazene, sincronize e consulte dados de aplicativos móveis da nuvem
Independentemente do tipo de aplicativo criado, você provavelmente vai gerar e processar dados. Os usuários do seu aplicativo têm altas expectativas. Eles querem que o aplicativo funcione de forma rápida e direta, em todas as circunstâncias. A maioria dos aplicativos também funciona em vários dispositivos. Você pode acessar seu aplicativo de um dispositivo móvel ou desktop. Vários usuários podem usar o aplicativo ao mesmo tempo e compartilhar dados com a expectativa de obter acesso imediato e em tempo real aos dados.

Os usuários do aplicativo nem sempre terão conectividade com a Internet. Os aplicativos são projetados e devem funcionar com ou sem uma conexão com a Internet. Os desenvolvedores devem escolher a solução certa para armazenar e sincronizar seus dados na nuvem para fornecer uma excelente experiência do cliente para seu aplicativo.

A Microsoft fornece uma variedade de serviços que eliminam a necessidade de criar servidores, escolher seu banco de dados ou se preocupar com escala ou segurança para fornecer a experiência mais rica possível. Esses serviços fornecem uma excelente experiência de desenvolvedor que permite armazenar dados de aplicativos na nuvem usando as APIs SQL ou NoSQL. Você também pode sincronizar dados em todos os dispositivos e permitir que o aplicativo funcione com ou sem uma conexão de rede para ajudar a criar aplicativos escalonáveis e robustos.

Use os seguintes serviços para gerenciar e armazenar dados de aplicativos móveis na nuvem.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center dados](/appcenter/data/) é um serviço de gerenciamento de dados. Ele permite que os aplicativos gerenciem, persistam e sincronizem dados de aplicativos na nuvem em diferentes dispositivos e plataformas em cenários online e offline. Baseado em Azure Cosmos DB, os dados de App Center são dimensionados conforme a sua base de usuários e o número de aplicativos crescem. Isso garante baixa latência, alta disponibilidade e escalabilidade alta para todos os seus dados.

**Principais funcionalidades**
- Provisionamento fácil para um novo banco de dados Azure Cosmos DB ou um banco de dados Azure Cosmos DB existente no portal do Visual Studio App Center.
- O suporte a banco de dados NoSQL para armazenar, sincronizar e consultar facilmente o aplicativo.
- Criado com base em Azure Cosmos DB, esse serviço herda a maioria dos principais recursos oferecidos pelo Azure Cosmos DB e pode ser dimensionado globalmente para atender às suas necessidades de negócios.
- Recursos de sincronização online e offline para sincronizar dados entre dispositivos.
- SDKs de cliente móvel que você pode usar para gerenciar facilmente dados de aplicativos privados.
- Suporte de plataforma para iOS, Android, Xamarin e reagir nativo.

**Referências**
- [Inscreva-se com Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introdução aos dados de App Center](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é um serviço de banco de dados multimodelo distribuído globalmente. Você pode usá-lo para criar aplicativos em escala de planeta. Com o Azure Cosmos DB, você pode dimensionar de forma elástica e independente a taxa de transferência e o armazenamento em qualquer número de regiões do Azure em todo o mundo. Você pode aproveitar o acesso a dados rápido e de um dígito em milissegundos usando suas superfícies de API favoritas. Essas superfícies incluem SQL, MongoDB, Cassandra, Tables ou Gremlin. Azure Cosmos DB fornece exclusivamente SLAs (contratos de nível de serviço) para taxa de transferência, latência, disponibilidade e consistência.

**Principais funcionalidades**
- O oferece suporte a uma ampla gama de APIs, que inclui a API do SQL (núcleo), API do Cassandra, API do MongoDB, API Gremlin e API de Tabela.
- A distribuição global completa Replica seus dados onde quer que os usuários estejam. Os usuários podem interagir com uma réplica dos dados mais próximos deles.
- Nenhum gerenciamento de esquema ou índice porque o mecanismo de banco de dados é totalmente independente de esquema.
- Presença regional onipresente porque Azure Cosmos DB está disponível em todas as regiões do Azure em todo o mundo, que inclui mais de 54 regiões na nuvem pública.
- Precisamente definidas, várias opções de consistência porque o protocolo de replicação de vários mestres do Azure Cosmos DB é cuidadosamente projetado para oferecer cinco opções de consistência bem definidas. Essas cinco opções são forte, desatualização limitada, sessão, prefixo consistente e eventual.
- 99,999% de disponibilidade para leituras e gravações.
- Programaticamente (ou por meio do portal do Azure) invocam o failover regional da sua conta de Azure Cosmos DB para garantir que seu aplicativo seja projetado para resistir a um desastre regional.
- Baixa latência garantida no 99 º percentil em todo o mundo.

**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Documentação do Azure Cosmos DB](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure
 O [banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) é um serviço gerenciado de banco de dados relacional de uso geral. Você pode usá-lo para criar uma camada de armazenamento de dados altamente disponível e de alto desempenho para aplicativos e soluções na nuvem do Azure.

**Principais funcionalidades**
- **Ferramentas e modelos de banco de dados elástico:** Com um banco de dados elástico, os desenvolvedores podem agrupar recursos entre um grupo de bancos de dados para dimensionamento. Para gerenciar administrativamente esses recursos, você envia um script como um trabalho. Em seguida, o banco de dados SQL executa o script em todos os bancos.
- **Alto desempenho:** Aplicativos de alta taxa de transferência podem aproveitar a versão mais recente. Ele fornece 25% mais potência de banco de dados Premium.
- **Backups, replicação e alta disponibilidade:** A replicação interna e um SLA com suporte da Microsoft no nível do banco de dados fornecem continuidade de aplicativos e proteção contra eventos catastróficos. A replicação geográfica ativa permite que você configure o failover e a restauração de autoatendimento, que fornecem controle total sobre "recuperação de ops". A restauração de dados está disponível em backups de dados de até 35 dias.
- **Manutenção quase zero:** O software automático faz parte do serviço. As réplicas internas do sistema ajudam a fornecer proteção de dados inerente, tempo de atividade do banco de dados e estabilidade do sistema. As réplicas do sistema são automaticamente movidas para novos computadores. Elas são provisionadas em tempo real à medida que as antigas falham.
- **Segurança:** O banco de dados SQL do Azure oferece um portfólio de recursos de segurança para atender às políticas de conformidade da organização ou das normas do setor:
    - A auditoria fornece aos desenvolvedores a capacidade de executar tarefas relacionadas à conformidade e obter conhecimento sobre atividades.
    - Os desenvolvedores e ti podem implementar políticas no nível de banco de dados para ajudar a limitar o acesso a dados confidenciais com segurança em nível de linha, mascaramento de dados dinâmicos e Transparent Data Encryption para o banco de dados SQL do Azure.
    - O banco de dados SQL do Azure é verificado pelos principais auditores de nuvem como parte do escopo das principais certificações e aprovações de conformidade do Azure, como HIPAA BAA, ISO/IEC 27001:2005, FedRAMP e cláusulas de modelo da UE.

**Referências**
- [Portal do Azure](https://portal.azure.com) 
- [Documentação da Base de Dados SQL do Azure](/azure/sql-database/) 

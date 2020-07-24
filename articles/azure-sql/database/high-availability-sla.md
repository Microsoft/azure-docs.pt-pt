---
title: Elevada disponibilidade
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Conheça a Base de Dados Azure SQL e o serviço SQL Managed Instance com elevadas capacidades de disponibilidade
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 04/02/2020
ms.openlocfilehash: cc0c4b6bc7dd340f17ac500c5d319a83370a2f2b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87033056"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Alta disponibilidade para Azure SQL Database e SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O objetivo da arquitetura de alta disponibilidade em Azure SQL Database e SQL Managed Instance é garantir que a sua base de dados está a funcionar no mínimo de 99,99% de tempo (Para mais informações sobre SLA específico para diferentes níveis, consulte [sLA para Azure SQL Database e SQL Managed Instance),](https://azure.microsoft.com/support/legal/sla/sql-database/)sem se preocupar com o impacto das operações de manutenção e interrupções. O Azure lida automaticamente com tarefas de manutenção críticas, tais como reparações, backups, atualizações windows e Azure SQL, bem como eventos não planeados, tais como hardware, software ou falhas de rede subjacentes.  Quando a base de dados subjacente na Base de Dados Azure SQL é corrigida ou falha, o tempo de inatividade não é percetível se [utilizar lógica de repetição](develop-overview.md#resiliency) na sua aplicação. A BASE de Dados SQL e a SQL Managed Instance podem recuperar rapidamente mesmo nas circunstâncias mais críticas, garantindo que os seus dados estão sempre disponíveis.

A solução de alta disponibilidade destina-se a garantir que os dados comprometidos nunca se percam devido a falhas, que as operações de manutenção não afetam a sua carga de trabalho e que a base de dados não será um único ponto de falha na sua arquitetura de software. Não existem janelas de manutenção ou tempos de inatividade que exijam que pare a carga de trabalho enquanto a base de dados é atualizada ou mantida.

Existem dois modelos arquitetónicos de alta disponibilidade:

- **Modelo de disponibilidade padrão** que se baseia numa separação entre computação e armazenamento.  Baseia-se na elevada disponibilidade e fiabilidade do nível de armazenamento remoto. Esta arquitetura visa aplicações empresariais orientadas para o orçamento que podem tolerar alguma degradação de desempenho durante as atividades de manutenção.
- **Modelo de disponibilidade premium** que se baseia num conjunto de processos de motor de base de dados. Baseia-se no facto de existir sempre um quórum de nós de motores de base de dados disponíveis. Esta arquitetura tem como alvo aplicações críticas de missão com elevado desempenho de IO, alta taxa de transação e garante o impacto mínimo de desempenho na sua carga de trabalho durante as atividades de manutenção.

A SQL Database e a SQL Managed Instance funcionam na versão mais recente e estável do motor de base de dados SQL Server e do sistema operativo Windows, e a maioria dos utilizadores não nota que as atualizações são realizadas continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilidade do nível de serviço básico, padrão e de finalidade geral

Os níveis de serviço Basic, Standard e General Purpose aproveitam a arquitetura de disponibilidade padrão tanto para o cálculo sem servidor como para o cálculo a provisionado. A figura a seguir mostra quatro nós diferentes com as camadas de computação e armazenamento separadas.

![Separação do cálculo e do armazenamento](./media/high-availability-sla/general-purpose-service-tier.png)

O modelo de disponibilidade padrão inclui duas camadas:

- Uma camada de computação apátrida que executa o `sqlservr.exe` processo e contém apenas dados transitórios e em cache, tais como TempDB, bases de dados de modelos no SSD anexado, e cache de plano, piscina tampão e piscina de colunas na memória. Este nó apátrida é operado pela Azure Service Fabric que `sqlservr.exe` inicializa, controla a saúde do nó, e executa falha em outro nó, se necessário.
- Uma camada de dados imponente com os ficheiros de base de dados (.mdf/.ldf) que são armazenados no armazenamento de Azure Blob. O armazenamento de blob azure tem disponibilidade de dados incorporado e recurso de redundância. Garante que todos os registos no ficheiro de registo ou página do ficheiro de dados serão preservados mesmo que o `sqlservr.exe` processo se despenhe.

Sempre que o motor de base de dados ou o sistema operativo for atualizado, ou se for detetada uma falha, a Azure Service Fabric deslocará o processo apátrida `sqlservr.exe` para outro nó de computação apátrida com capacidade livre suficiente. Os dados no armazenamento da Azure Blob não são afetados pelo movimento, e os ficheiros de dados/registo são anexados ao processo recém-inicializado. `sqlservr.exe` Este processo garante uma disponibilidade de 99,99%, mas uma carga de trabalho pesada pode sofrer alguma degradação do desempenho durante a transição desde que o novo `sqlservr.exe` processo começa com cache frio.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilidade do nível de serviço Premium e Business Critical

Os níveis de serviço Premium e Business Critical alavancam o modelo de disponibilidade Premium, que integra recursos compute `sqlservr.exe` (processo) e armazenamento (SSD anexado localmente) num único nó. A alta disponibilidade é conseguida replicando tanto o cálculo como o armazenamento para nós adicionais criando um cluster de três a quatro nós.

![Aglomerado de nosdes de motor de base de dados](./media/high-availability-sla/business-critical-service-tier.png)

Os ficheiros de base de dados subjacentes (.mdf/.ldf) são colocados no armazenamento SSD anexado para fornecer IO de latência muito baixa à sua carga de trabalho. A alta disponibilidade é implementada utilizando uma tecnologia semelhante ao SQL Server [Always On availability groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). O cluster inclui uma única réplica primária que é acessível para a leitura-escrita do cliente, e até três réplicas secundárias (computação e armazenamento) contendo cópias de dados. O nó primário empurra constantemente as alterações nos nós secundários em ordem e assegura que os dados são sincronizados a pelo menos uma réplica secundária antes de efetizar cada transação. Este processo garante que se o nó primário se despenhar por qualquer motivo, há sempre um nó sincronizado totalmente sincronizado para falhar. A falha é iniciada pelo Azure Service Fabric. Uma vez que a réplica secundária se torne o novo nó primário, outra réplica secundária é criada para garantir que o cluster tem nóns suficientes (conjunto quórum). Uma vez concluída a falha, as ligações Azure SQL são automaticamente redirecionadas para o novo nó primário.

Como benefício extra, o modelo de disponibilidade premium inclui a capacidade de redirecionar as ligações Azure SQL apenas de leitura para uma das réplicas secundárias. Esta funcionalidade [chama-se "Escala de Leitura".](read-scale-out.md) Fornece uma capacidade de computação adicional 100% adicional, sem custos adicionais, para operações de leitura descarregadas, como cargas de trabalho analíticas, a partir da réplica primária.

## <a name="hyperscale-service-tier-availability"></a>Disponibilidade de nível de serviço de hiperescala

A arquitetura de nível de serviço de hiperescala é descrita na [arquitetura de funções distribuídas](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture) e só está atualmente disponível para base de dados SQL, e não SQL Managed Instance.

![Arquitetura funcional de hiperescala](./media/high-availability-sla/hyperscale-architecture.png)

O modelo de disponibilidade em Hiperescala inclui quatro camadas:

- Uma camada de computação apátrida que executa os `sqlservr.exe` processos e contém apenas dados transitórios e em cache, tais como cache RBPEX não-cobrindo, TempDB, base de dados de modelos, etc. no SSD anexo, e cache de plano, piscina tampão e piscina de colunas na memória. Esta camada apátrida inclui a réplica do cálculo primário e opcionalmente uma série de réplicas de computação secundária que podem servir como alvos de failover.
- Uma camada de armazenamento apátrida formada por servidores de página. Esta camada é o motor de armazenamento distribuído para os `sqlservr.exe` processos em execução nas réplicas computacional. Cada servidor de página contém apenas dados transitórios e em cache, tais como a cobertura da cache RBPEX no SSD anexado e páginas de dados em cache na memória. Cada servidor de página tem um servidor de página emparelhado numa configuração ativa para fornecer equilíbrio de carga, redundância e alta disponibilidade.
- Uma camada de armazenamento de registo de transações estatal formada pelo nó de cálculo que executa o processo de serviço log, a zona de aterragem de registo de transações e armazenamento de registo de transações a longo prazo. Zona de aterragem e armazenamento de longo prazo utilizam o Azure Storage, que fornece disponibilidade e [redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) para registo de transações, garantindo a durabilidade dos dados para transações comprometidas.
- Uma camada de armazenamento de dados com os ficheiros de base de dados (.mdf/.ndf) que são armazenados no Azure Storage e são atualizados pelos servidores de página. Esta camada utiliza recursos de disponibilidade de dados e [redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) do Azure Storage. Garante que todas as páginas de um ficheiro de dados serão preservadas mesmo que os processos noutras camadas de arquitetura hyperscale caiam, ou se os nós de computação falharem.

Os nós computativos em todas as camadas de Hiperescala funcionam no Azure Service Fabric, que controla a saúde de cada nó e executa falhas para os nós saudáveis disponíveis, se necessário.

Para obter mais informações sobre a elevada disponibilidade em Hiperescala, consulte [Base de Dados Alta Disponibilidade em Hiperescala.](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale)

## <a name="zone-redundant-configuration"></a>Configuração redundante de zona

Por padrão, o cluster de nós para o modelo de disponibilidade premium é criado no mesmo datacenter. Com a introdução de Zonas de [Disponibilidade Azure,](../../availability-zones/az-overview.md)a SQL Database pode colocar diferentes réplicas da base de dados Business Critical em diferentes zonas de disponibilidade na mesma região. Para eliminar um único ponto de falha, o anel de controlo também é duplicado em várias zonas como três anéis de gateway (GW). O encaminhamento para um anel de gateway específico é controlado pelo [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM). Uma vez que a configuração redundante da zona nos níveis de serviço Premium ou Business Critical não cria redundância de base de dados adicional, pode permitir-lhe sem custos adicionais. Ao selecionar uma configuração redundante de zona, pode tornar as suas bases de dados Premium ou Business Critical resilientes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem alterações na lógica da aplicação. Também pode converter quaisquer bases de dados premium ou business critical existentes para a configuração redundante da zona.

Como as bases de dados redundantes da zona têm réplicas em diferentes datacenters com alguma distância entre eles, o aumento da latência da rede pode aumentar o tempo de compromisso e, assim, impactar o desempenho de algumas cargas de trabalho OLTP. Pode sempre voltar à configuração de uma única zona desativando a definição de redundância de zona. Este processo é uma operação online semelhante à atualização regular do nível de serviço. No final do processo, a base de dados ou piscina é migrada de um anel redundante de uma zona para um anel de uma única zona ou vice-versa.

> [!IMPORTANT]
> As bases de dados redundantes da zona e as piscinas elásticas são atualmente suportadas apenas nos níveis de serviço Premium e Business Critical em regiões selecionadas. Ao utilizar o nível Business Critical, a configuração redundante da zona só está disponível quando o hardware de computação Gen5 é selecionado. Para obter informações atualizadas sobre as regiões que suportam bases de dados redundantes da zona, consulte [o apoio dos Serviços por região.](../../availability-zones/az-region.md)

> [!NOTE]
> Esta funcionalidade não está disponível em SQL Managed Instance.

A versão redundante da zona da arquitetura de alta disponibilidade é ilustrada pelo seguinte diagrama:

![zona de arquitetura de alta disponibilidade redundante](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Recuperação acelerada da base de dados (ADR)

[A Accelerated Database Recovery (ADR)](../accelerated-database-recovery.md) é uma nova funcionalidade de motor de base de dados que melhora consideravelmente a disponibilidade da base de dados, especialmente na presença de transações de longa duração. A ADR está atualmente disponível para Azure SQL Database, Azure SQL Managed Instance e Azure SQL Data Warehouse.

## <a name="testing-application-fault-resiliency"></a>Teste de resiliência da falha da aplicação

A alta disponibilidade é uma parte fundamental da plataforma SQL Database e SQL Managed Instance que funciona de forma transparente para a sua aplicação de base de dados. No entanto, reconhecemos que poderá querer testar como as operações automáticas de failover iniciadas durante eventos planeados ou não planeados teriam impacto na aplicação antes de a implementar na produção. Pode chamar uma API especial para reiniciar uma base de dados ou uma piscina elástica, o que por sua vez irá desencadear uma falha. No caso de uma base de dados redundante ou piscina elástica, a chamada da API resultaria na reorientação das ligações dos clientes para a nova primária numa Zona de Disponibilidade diferente da Zona de Disponibilidade da antiga primária. Assim, além de testar como o failover impacta as sessões de base de dados existentes, também pode verificar se altera o desempenho de ponta a ponta devido a alterações na latência da rede. Como a operação de reinício é intrusiva e um grande número deles pode stressar a plataforma, apenas uma chamada de failover é permitida a cada 30 minutos para cada base de dados ou piscina elástica.

Uma falha pode ser iniciada usando REST API ou PowerShell. Para REST API, consulte [a falha da base de dados](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) e a falha da piscina [elástica.](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover) Para PowerShell, consulte [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) e [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). As chamadas REST API também podem ser feitas a partir de Azure CLI usando o comando [de repouso az.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest)

> [!IMPORTANT]
> O comando Failover não está atualmente disponível no nível de serviço de Hiperescala e para Instância Gerida.

## <a name="conclusion"></a>Conclusão

A Azure SQL Database e Azure SQL Managed Instance apresentam uma solução de alta disponibilidade incorporada, que está profundamente integrada com a plataforma Azure. Depende do Tecido de Serviço para deteção e recuperação de falhas, no armazenamento de Azure Blob para proteção de dados, e em Zonas de Disponibilidade para maior tolerância a falhas (como mencionado anteriormente no documento ainda não aplicável ao Azure SQL Managed Instance). Além disso, a SQL Database e a SQL Managed Instance aproveitam a tecnologia de grupo de disponibilidade Always On a partir da instância sql Server para replicação e failover. A combinação destas tecnologias permite que as aplicações percebam plenamente os benefícios de um modelo de armazenamento misto e suportem os SLAs mais exigentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as Zonas de Disponibilidade Azure](../../availability-zones/az-overview.md)
- Saiba mais sobre [o Tecido de Serviço](../../service-fabric/service-fabric-overview.md)
- Saiba mais sobre [o Gestor de Tráfego da Azure](../../traffic-manager/traffic-manager-overview.md)
- Para mais opções de alta disponibilidade e recuperação de desastres, consulte [Continuidade de Negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md)

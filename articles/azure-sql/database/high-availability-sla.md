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
author: emlisa
ms.author: emlisa
ms.reviewer: sstein, emlisa
ms.date: 10/28/2020
ms.openlocfilehash: 1c210eab0332d01fc6514edc790d729172ed2174
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889064"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Alta disponibilidade para Azure SQL Database e SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O objetivo da arquitetura de alta disponibilidade em Azure SQL Database e SQL Managed Instance é garantir que a sua base de dados está a funcionar no mínimo de 99,99% de tempo (Para mais informações sobre SLA específico para diferentes níveis, consulte [sLA para Azure SQL Database e SQL Managed Instance),](https://azure.microsoft.com/support/legal/sla/sql-database/)sem se preocupar com o impacto das operações de manutenção e interrupções. O Azure lida automaticamente com tarefas de manutenção críticas, tais como reparações, backups, atualizações windows e Azure SQL, bem como eventos não planeados, tais como hardware, software ou falhas de rede subjacentes.  Quando a base de dados subjacente na Base de Dados Azure SQL é corrigida ou falha, o tempo de inatividade não é percetível se [utilizar lógica de repetição](develop-overview.md#resiliency) na sua aplicação. A BASE de Dados SQL e a SQL Managed Instance podem recuperar rapidamente mesmo nas circunstâncias mais críticas, garantindo que os seus dados estão sempre disponíveis.

A solução de alta disponibilidade destina-se a garantir que os dados comprometidos nunca se percam devido a falhas, que as operações de manutenção não afetam a sua carga de trabalho e que a base de dados não será um único ponto de falha na sua arquitetura de software. Não existem janelas de manutenção ou tempos de inatividade que exijam que pare a carga de trabalho enquanto a base de dados é atualizada ou mantida.

Existem dois modelos arquitetónicos de alta disponibilidade:

- **Modelo de disponibilidade padrão** que se baseia numa separação entre computação e armazenamento.  Baseia-se na elevada disponibilidade e fiabilidade do nível de armazenamento remoto. Esta arquitetura visa aplicações empresariais orientadas para o orçamento que podem tolerar alguma degradação de desempenho durante as atividades de manutenção.
- **Modelo de disponibilidade premium** que se baseia num conjunto de processos de motor de base de dados. Baseia-se no facto de existir sempre um quórum de nós de motores de base de dados disponíveis. Esta arquitetura tem como alvo aplicações críticas de missão com elevado desempenho de IO, alta taxa de transação e garante o impacto mínimo de desempenho na sua carga de trabalho durante as atividades de manutenção.

A SQL Database e a SQL Managed Instance funcionam na versão mais recente e estável do motor de base de dados SQL Server e do sistema operativo Windows, e a maioria dos utilizadores não nota que as atualizações são realizadas continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Nível de serviço básico, padrão e de finalidade geral disponibilidade localmente redundante

Os níveis de serviço Basic, Standard e General Purpose aproveitam a arquitetura de disponibilidade padrão tanto para o cálculo sem servidor como para o cálculo a provisionado. A figura a seguir mostra quatro nós diferentes com as camadas de computação e armazenamento separadas.

![Separação do cálculo e do armazenamento](./media/high-availability-sla/general-purpose-service-tier.png)

O modelo de disponibilidade padrão inclui duas camadas:

- Uma camada de computação apátrida que executa o `sqlservr.exe` processo e contém apenas dados transitórios e em cache, tais como TempDB, bases de dados de modelos no SSD anexado, e cache de plano, piscina tampão e piscina de colunas na memória. Este nó apátrida é operado pela Azure Service Fabric que `sqlservr.exe` inicializa, controla a saúde do nó, e executa falha em outro nó, se necessário.
- Uma camada de dados imponente com os ficheiros de base de dados (.mdf/.ldf) que são armazenados no armazenamento de Azure Blob. O armazenamento de blob azure tem disponibilidade de dados incorporado e recurso de redundância. Garante que todos os registos no ficheiro de registo ou página do ficheiro de dados serão preservados mesmo que o `sqlservr.exe` processo se despenhe.

Sempre que o motor de base de dados ou o sistema operativo for atualizado, ou se for detetada uma falha, a Azure Service Fabric deslocará o processo apátrida `sqlservr.exe` para outro nó de computação apátrida com capacidade livre suficiente. Os dados no armazenamento da Azure Blob não são afetados pelo movimento, e os ficheiros de dados/registo são anexados ao processo recém-inicializado. `sqlservr.exe` Este processo garante uma disponibilidade de 99,99%, mas uma carga de trabalho pesada pode sofrer alguma degradação do desempenho durante a transição desde que o novo `sqlservr.exe` processo começa com cache frio.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Disponibilidade redundante da zona de nível de serviço para fins gerais (pré-visualização)

Configuração redundante de zona para o nível de serviço de finalidade geral utiliza [Zonas de Disponibilidade Azure](../../availability-zones/az-overview.md)   para replicar bases de dados em vários locais físicos dentro de uma região de Azure.Ao selecionar a redundância de zona, pode tornar as suas novas bases de dados únicas e piscinas elásticas resistentes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem alterações na lógica da aplicação.

A configuração redundante da zona para o nível de finalidade geral tem duas camadas:  

- Uma camada de dados imponente com os ficheiros de base de dados (.mdf/.ldf) que são armazenados em ZRS PFS [(parte de ficheiros premium](../../storage/files/storage-how-to-create-file-share.md)de armazenamento redundante de zona . Utilizando [o armazenamento redundante de zona,](../../storage/common/storage-redundancy.md) os ficheiros de dados e registos são copiados sincronizadamente em três zonas de disponibilidade de Azure fisicamente isoladas.
- Uma camada de computação apátrida que executa o processo sqlservr.exe e contém apenas dados transitórios e em cache, tais como TempDB, bases de dados de modelos no SSD anexado, e cache de plano, piscina tampão e piscina de colunas na memória. Este nó apátrida é operado pela Azure Service Fabric que inicializa sqlservr.exe, controla a saúde do nó, e executa falha em outro nó, se necessário. Para bases de dados de finalidades gerais redundantes de zona, os nós com capacidade suplente estão prontamente disponíveis noutras Zonas de Disponibilidade para falha.

A versão redundante da zona da arquitetura de alta disponibilidade para o nível de serviço para fins gerais é ilustrada pelo seguinte diagrama:

![Configuração redundante de zona para fins gerais](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> A configuração redundante da zona só está disponível quando o hardware de computação Gen5 é selecionado. Esta funcionalidade não está disponível em SQL Managed Instance. A configuração redundante da zona para fins gerais só está disponível nas seguintes regiões: Leste dos EUA, Leste dos EUA 2, Oeste dos EUA 2, Norte da Europa, Europa Ocidental, Sudeste Asiático, Austrália Oriental, Japão Leste, Reino Unido Sul e França Central.

> [!NOTE]
> Bases de dados de Finalidade Geral com um tamanho de 80 vcore podem experimentar degradação de desempenho com configuração redundante de zona. Além disso, operações como cópia de backup, restauro, cópia de base de dados e configuração de relações Geo-DR podem experimentar um desempenho mais lento para qualquer base de dados maior do que 1 TB. 
> 
> [!NOTE]
> A pré-visualização não é abrangida pela Instância Reservada

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Nível de serviço Premium e Business Critical disponível localmente

Os níveis de serviço Premium e Business Critical alavancam o modelo de disponibilidade Premium, que integra recursos compute `sqlservr.exe` (processo) e armazenamento (SSD anexado localmente) num único nó. A alta disponibilidade é conseguida replicando tanto o cálculo como o armazenamento para nós adicionais criando um cluster de três a quatro nós.

![Aglomerado de nosdes de motor de base de dados](./media/high-availability-sla/business-critical-service-tier.png)

Os ficheiros de base de dados subjacentes (.mdf/.ldf) são colocados no armazenamento SSD anexado para fornecer IO de latência muito baixa à sua carga de trabalho. A alta disponibilidade é implementada utilizando uma tecnologia semelhante ao SQL Server [Always On availability groups](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). O cluster inclui uma única réplica primária que é acessível para a leitura-escrita do cliente, e até três réplicas secundárias (computação e armazenamento) contendo cópias de dados. O nó primário empurra constantemente as alterações nos nós secundários em ordem e assegura que os dados são sincronizados a pelo menos uma réplica secundária antes de efetizar cada transação. Este processo garante que se o nó primário se despenhar por qualquer motivo, há sempre um nó sincronizado totalmente sincronizado para falhar. A falha é iniciada pelo Azure Service Fabric. Uma vez que a réplica secundária se torne o novo nó primário, outra réplica secundária é criada para garantir que o cluster tem nóns suficientes (conjunto quórum). Uma vez concluída a falha, as ligações Azure SQL são automaticamente redirecionadas para o novo nó primário.

Como benefício extra, o modelo de disponibilidade premium inclui a capacidade de redirecionar as ligações Azure SQL apenas de leitura para uma das réplicas secundárias. Esta funcionalidade [chama-se "Escala de Leitura".](read-scale-out.md) Fornece uma capacidade de computação adicional 100% adicional, sem custos adicionais, para operações de leitura descarregadas, como cargas de trabalho analíticas, a partir da réplica primária.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Disponibilidade redundante de nível de serviço Premium e Business Critical 

Por padrão, o cluster de nós para o modelo de disponibilidade premium é criado no mesmo datacenter. Com a introdução de Zonas de [Disponibilidade Azure,](../../availability-zones/az-overview.md)a SQL Database pode colocar diferentes réplicas da base de dados Business Critical em diferentes zonas de disponibilidade na mesma região. Para eliminar um único ponto de falha, o anel de controlo também é duplicado em várias zonas como três anéis de gateway (GW). O encaminhamento para um anel de gateway específico é controlado pelo [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM). Uma vez que a configuração redundante da zona nos níveis de serviço Premium ou Business Critical não cria redundância de base de dados adicional, pode permitir-lhe sem custos adicionais. Ao selecionar uma configuração redundante de zona, pode tornar as suas bases de dados Premium ou Business Critical resilientes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem alterações na lógica da aplicação. Também pode converter quaisquer bases de dados premium ou business critical existentes para a configuração redundante da zona.

Como as bases de dados redundantes da zona têm réplicas em diferentes datacenters com alguma distância entre eles, o aumento da latência da rede pode aumentar o tempo de compromisso e, assim, impactar o desempenho de algumas cargas de trabalho OLTP. Pode sempre voltar à configuração de uma única zona desativando a definição de redundância de zona. Este processo é uma operação online semelhante à atualização regular do nível de serviço. No final do processo, a base de dados ou piscina é migrada de um anel redundante de uma zona para um anel de uma única zona ou vice-versa.

> [!IMPORTANT]
> Ao utilizar o nível Business Critical, a configuração redundante da zona só está disponível quando o hardware de computação Gen5 é selecionado. Para obter informações atualizadas sobre as regiões que suportam bases de dados redundantes da zona, consulte [o apoio dos Serviços por região.](../../availability-zones/az-region.md)

> [!NOTE]
> Esta funcionalidade não está disponível em SQL Managed Instance.

A versão redundante da zona da arquitetura de alta disponibilidade é ilustrada pelo seguinte diagrama:

![zona de arquitetura de alta disponibilidade redundante](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Disponibilidade de nível de serviço de hiperescala

A arquitetura de nível de serviço de hiperescala é descrita na [arquitetura de funções distribuídas](./service-tier-hyperscale.md#distributed-functions-architecture) e só está atualmente disponível para base de dados SQL, e não SQL Managed Instance.

![Arquitetura funcional de hiperescala](./media/high-availability-sla/hyperscale-architecture.png)

O modelo de disponibilidade em Hiperescala inclui quatro camadas:

- Uma camada de computação apátrida que executa os `sqlservr.exe` processos e contém apenas dados transitórios e em cache, tais como cache RBPEX não-cobrindo, TempDB, base de dados de modelos, etc. no SSD anexo, e cache de plano, piscina tampão e piscina de colunas na memória. Esta camada apátrida inclui a réplica do cálculo primário e opcionalmente uma série de réplicas de computação secundária que podem servir como alvos de failover.
- Uma camada de armazenamento apátrida formada por servidores de página. Esta camada é o motor de armazenamento distribuído para os `sqlservr.exe` processos em execução nas réplicas computacional. Cada servidor de página contém apenas dados transitórios e em cache, tais como a cobertura da cache RBPEX no SSD anexado e páginas de dados em cache na memória. Cada servidor de página tem um servidor de página emparelhado numa configuração ativa para fornecer equilíbrio de carga, redundância e alta disponibilidade.
- Uma camada de armazenamento de registo de transações estatal formada pelo nó de cálculo que executa o processo de serviço log, a zona de aterragem de registo de transações e armazenamento de registo de transações a longo prazo. Zona de aterragem e armazenamento de longo prazo utilizam o Azure Storage, que fornece disponibilidade e [redundância](../../storage/common/storage-redundancy.md) para registo de transações, garantindo a durabilidade dos dados para transações comprometidas.
- Uma camada de armazenamento de dados com os ficheiros de base de dados (.mdf/.ndf) que são armazenados no Azure Storage e são atualizados por servidores de página. Esta camada utiliza recursos de disponibilidade de dados e [redundância](../../storage/common/storage-redundancy.md) do Azure Storage. Garante que todas as páginas de um ficheiro de dados serão preservadas mesmo que os processos noutras camadas de arquitetura hyperscale caiam, ou se os nós de computação falharem.

Os nós computativos em todas as camadas de Hiperescala funcionam no Azure Service Fabric, que controla a saúde de cada nó e executa falhas para os nós saudáveis disponíveis, se necessário.

Para obter mais informações sobre a elevada disponibilidade em Hiperescala, consulte [Base de Dados Alta Disponibilidade em Hiperescala.](./service-tier-hyperscale.md#database-high-availability-in-hyperscale)


## <a name="accelerated-database-recovery-adr"></a>Recuperação acelerada da base de dados (ADR)

[A Accelerated Database Recovery (ADR)](../accelerated-database-recovery.md) é uma nova funcionalidade de motor de base de dados que melhora consideravelmente a disponibilidade da base de dados, especialmente na presença de transações de longa duração. A ADR está atualmente disponível para Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics.

## <a name="testing-application-fault-resiliency"></a>Teste de resiliência da falha da aplicação

A elevada disponibilidade é uma parte fundamental da plataforma SQL Managed Instance e da Base de Dados SQL que funcionam de forma transparente na aplicação de base de dados. No entanto, reconhecemos que possa querer testar de que forma as operações de ativação pós-falha automáticas e iniciadas durante eventos planeados ou não planeados afetam a aplicação antes de a implementar para produção. Pode desencadear manualmente uma falha, chamando uma API especial para reiniciar uma base de dados, uma piscina elástica ou uma instância gerida. No caso de uma base de dados redundante ou piscina elástica, a chamada da API resultaria na reorientação das ligações dos clientes para a nova primária numa Zona de Disponibilidade diferente da Zona de Disponibilidade da antiga primária. Assim, além de testar como o failover impacta as sessões de base de dados existentes, também pode verificar se altera o desempenho de ponta a ponta devido a alterações na latência da rede. Como a operação de reinício é intrusiva e um grande número deles pode stressar a plataforma, apenas uma chamada de failover é permitida a cada 15 minutos para cada base de dados, piscina elástica ou instância gerida.

Uma falha pode ser iniciada usando PowerShell, REST API ou Azure CLI:

|Tipo de implantação|PowerShell|API REST| CLI do Azure|
|:---|:---|:---|:---|
|Base de Dados|[Invocar-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Falha na base de dados](/rest/api/sql/databases/failover)|[az descanso](/cli/azure/reference-index#az-rest) pode ser usado para invocar uma chamada de API REST de Azure CLI|
|Conjunto elástico|[Invocar-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Falha na piscina elástica](/rest/api/sql/elasticpools/failover)|[az descanso](/cli/azure/reference-index#az-rest) pode ser usado para invocar uma chamada de API REST de Azure CLI|
|Instância Gerida|[Invocar-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Casos Geridos - Failover](/rest/api/sql/managed%20instances%20-%20failover/failover)|[az sql mi failover](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> O comando Failover não está disponível para réplicas secundárias legíveis de bases de dados de Hiperescala.

## <a name="conclusion"></a>Conclusão

A Azure SQL Database e Azure SQL Managed Instance apresentam uma solução de alta disponibilidade incorporada, que está profundamente integrada com a plataforma Azure. Depende do Tecido de Serviço para deteção e recuperação de falhas, no armazenamento de Azure Blob para proteção de dados, e em Zonas de Disponibilidade para maior tolerância a falhas (como mencionado anteriormente no documento ainda não aplicável ao Azure SQL Managed Instance). Além disso, a SQL Database e a SQL Managed Instance aproveitam a tecnologia de grupo de disponibilidade Always On a partir da instância sql Server para replicação e failover. A combinação destas tecnologias permite que as aplicações percebam plenamente os benefícios de um modelo de armazenamento misto e suportem os SLAs mais exigentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as Zonas de Disponibilidade Azure](../../availability-zones/az-overview.md)
- Saiba mais sobre [o Tecido de Serviço](../../service-fabric/service-fabric-overview.md)
- Saiba mais sobre [o Gestor de Tráfego da Azure](../../traffic-manager/traffic-manager-overview.md)
- Saiba [como iniciar uma falha manual na SQL Managed Instance](../managed-instance/user-initiated-failover.md)
- Para mais opções de alta disponibilidade e recuperação de desastres, consulte [Continuidade de Negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md)

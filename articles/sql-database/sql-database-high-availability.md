---
title: Elevada disponibilidade
description: Conheça o serviço de base de dados Azure SQL de elevadas capacidades e funcionalidades de disponibilidade
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/14/2019
ms.openlocfilehash: b560cee23855d1c0e8a7b3c2cb9d82c184a1ebf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269656"
---
# <a name="high-availability-and-azure-sql-database"></a>Base de dados Azure SQL

O objetivo da arquitetura de alta disponibilidade na Base de Dados Azure SQL é garantir que a sua base de dados está a funcionar no mínimo 99,99% do tempo (Para mais informações sobre SLA específicos para diferentes camadas, consulte o SLA para a Base de [Dados Azure SQL),](https://azure.microsoft.com/support/legal/sla/sql-database/)sem se preocupar com o impacto das operações de manutenção e interrupções. O Azure lida automaticamente com tarefas críticas de manutenção, tais como patching, backups, atualizações windows e SQL, bem como eventos não planeados, tais como falhas de hardware, software ou rede subjacentes.  Quando a instância SQL subjacente é remendada ou falha, o tempo de inatividade não é percetível se utilizar uma lógica de [retry](sql-database-develop-overview.md#resiliency) na sua aplicação. A Base de Dados Azure SQL pode recuperar rapidamente mesmo nas circunstâncias mais críticas, garantindo que os seus dados estão sempre disponíveis.

A solução de alta disponibilidade foi concebida para garantir que os dados cometidos nunca se percam devido a falhas, que as operações de manutenção não afetam a sua carga de trabalho, e que a base de dados não será um único ponto de falha na arquitetura do seu software. Não existem janelas de manutenção ou horários de paragem que devam exigir que pare a carga de trabalho enquanto a base de dados é atualizada ou mantida. 

Existem dois modelos arquitetônicos de alta disponibilidade que são usados na Base de Dados Azure SQL:

- Modelo de disponibilidade padrão que se baseia numa separação de computação e armazenamento.  Baseia-se na elevada disponibilidade e fiabilidade do nível de armazenamento remoto. Esta arquitetura tem como alvo aplicações empresariais orientadas para o orçamento que podem tolerar alguma degradação do desempenho durante as atividades de manutenção.
- Modelo de disponibilidade premium que se baseia num conjunto de processos de motor de base de dados. Baseia-se no facto de existir sempre um quórum de nódos os motores de base de dados disponíveis. Esta arquitetura destina-se a aplicações críticas da missão com elevado desempenho em IO, alta taxa de transação e garante o mínimo impacto de desempenho na sua carga de trabalho durante as atividades de manutenção.

A Base de Dados Azure SQL funciona na versão mais recente e estável do Motor de Base de Dados do Servidor SQL e do Windows OS, e a maioria dos utilizadores não nota que as atualizações são executadas continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilidade de nível de serviço básico, standard e geral

Estes níveis de serviço alavancam a arquitetura de disponibilidade padrão. A figura que se segue mostra quatro nós diferentes com a computação separada e camadas de armazenamento.

![Separação de cálculo e armazenamento](media/sql-database-high-availability/general-purpose-service-tier.png)

O modelo de disponibilidade padrão inclui duas camadas:

- Uma camada de cálculo apátrida que executa o `sqlservr.exe` processo e contém apenas dados transitórios e cached, como TempDB, bases de dados de modelos no SSD anexado, e cache de plano, piscina tampão e piscina de colunas na memória. Este nó apátrida é operado pela Azure Service Fabric que `sqlservr.exe`inicializa, controla a saúde do nó e executa a falha em outro nó, se necessário.
- Uma camada de dados audaz com os ficheiros de base de dados (.mdf/.ldf) que são armazenados no armazenamento da Blob Azure. O armazenamento de blob Azure tem disponibilidade de dados incorporados e recurso de redundância. Garante que todos os registos no ficheiro de registo ou página do ficheiro de dados serão preservados mesmo que o processo do SQL Server se desfaça.

Sempre que o motor de base de dados ou o sistema operativo for atualizado, ou se for detetada uma falha, o Tecido de Serviço Azure deslocará o processo apátrida do SQL Server para outro nó de cálculo apátrida com capacidade gratuita suficiente. Os dados no armazenamento do Blob Azure não são afetados pelo movimento, e os ficheiros de dados/registos estão ligados ao processo do Servidor SQL recém-inicializado. Este processo garante 99,99% de disponibilidade, mas uma carga de trabalho pesada pode experimentar alguma degradação do desempenho durante a transição desde que a nova instância do SQL Server começa com cache frio.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilidade de nível de serviço Premium e Business Critical

Os níveis de serviço Premium e Business Critical aproveitam o modelo de disponibilidade Premium, que integra os recursos de computação (processo do Motor de Base de Dados do Servidor SQL) e o armazenamento (SSD ligado localmente) num único nó. A elevada disponibilidade é conseguida replicando tanto a computação como o armazenamento a nós adicionais criando um cluster de três a quatro nós. 

![Aglomerado de nós de motor de base de dados](media/sql-database-high-availability/business-critical-service-tier.png)

Os ficheiros de base de dados subjacentes (.mdf/.ldf) são colocados no armazenamento SSD anexado para fornecer iO de latência muito baixa à sua carga de trabalho. A alta disponibilidade é implementada utilizando uma tecnologia semelhante aos [Grupos](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)de Disponibilidade do Servidor SQL Always On . O cluster inclui uma única réplica primária (processo SQL Server) acessível para cargas de trabalho dos clientes de leitura e até três réplicas secundárias (computação e armazenamento) contendo cópias de dados. O nó primário pressiona constantemente as alterações aos nós secundários em ordem e assegura que os dados são sincronizados para pelo menos uma réplica secundária antes de cometer cada transação. Este processo garante que se o nó primário falhar por qualquer motivo, há sempre um nó totalmente sincronizado para não conseguir. A falha é iniciada pelo Tecido de Serviço Azure. Uma vez que a réplica secundária se torna o novo nó primário, outra réplica secundária é criada para garantir que o cluster tem nós suficientes (conjunto de quórum). Uma vez concluída a falha, as ligações SQL são automaticamente redirecionadas para o novo nó primário.

Como benefício extra, o modelo de disponibilidade premium inclui a capacidade de redirecionar ligações SQL apenas de leitura para uma das réplicas secundárias. Esta funcionalidade [chama-se Read Scale-Out](sql-database-read-scale-out.md). Fornece 100% de capacidade de computação adicional sem custos adicionais para operações de leitura descarregadas, como cargas de trabalho analíticas, a partir da réplica primária.

## <a name="hyperscale-service-tier-availability"></a>Disponibilidade de nível de serviço de hiperescala

A arquitetura de nível de serviço Hyperscale é descrita na arquitetura de [funções distribuídas.](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture) 

![Arquitetura funcional de hiperescala](./media/sql-database-hyperscale/hyperscale-architecture.png)

O modelo de disponibilidade em Hiperescala inclui quatro camadas:

- Uma camada de cálculo apátrida que executa os `sqlservr.exe` processos e contém apenas dados transitórios e cached, tais como cache RBPEX não-coberto, TempDB, base de dados de modelos, etc. no SSD anexado, e cache de plano, piscina tampão e piscina de colunas na memória. Esta camada apátrida inclui a réplica primária do cálculo e opcionalmente uma série de réplicas de computação secundária que podem servir como alvos de failover.
- Uma camada de armazenamento apátrida formada por servidores de página. Esta camada é o motor `sqlservr.exe` de armazenamento distribuído para os processos em execução nas réplicas computacionais. Cada servidor de página contém apenas dados transitórios e cached, tais como cobrir a cache RBPEX no SSD anexado, e páginas de dados em cache na memória. Cada servidor de página tem um servidor de página emparelhado numa configuração ativa para fornecer equilíbrio de carga, redundância e alta disponibilidade.
- Uma camada de armazenamento de registo de transações imponente formada pelo nó computacional que executa o processo de serviço de Registo, a zona de desembarque de registo de transações e armazenamento a longo prazo do registo de transações. Zona de desembarque e armazenamento de longo prazo Utilização Azure Storage, que fornece disponibilidade e [redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) para registo de transações, garantindo a durabilidade dos dados para transações comprometidas.
- Uma camada de armazenamento de dados aguçada com os ficheiros de base de dados (.mdf/.ndf) que são armazenados no Armazenamento Azure e são atualizados por servidores de página. Esta camada utiliza funcionalidades de disponibilidade de dados e [de redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) do Armazenamento Azure. Garante que cada página de um ficheiro de dados será preservada mesmo que os processos noutras camadas de arquitetura hyperscale falhem, ou se os nós de computação falharem.

Os nós computacionais em todas as camadas de hiperescala funcionam no Tecido de Serviço Azure, que controla a saúde de cada nó e executa falhas nos nós saudáveis disponíveis, se necessário.

Para obter mais informações sobre a elevada disponibilidade em Hiperescala, consulte [A Alta Disponibilidade da Base de Dados em Hiperescala](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Configuração redundante da zona

Por padrão, o cluster de nós para o modelo de disponibilidade premium é criado no mesmo datacenter. Com a introdução de Zonas de [Disponibilidade Azure,](../availability-zones/az-overview.md)a Base de Dados SQL pode colocar diferentes réplicas da base de dados Business Critical para diferentes zonas de disponibilidade na mesma região. Para eliminar um único ponto de falha, o anel de controlo também é duplicado em várias zonas como três anéis de gateway (GW). O encaminhamento para um anel de gateway específico é controlado pelo [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (MULTIM). Uma vez que a configuração redundante da zona nos níveis de serviço Premium ou Business Critical não cria redundância adicional na base de dados, pode permitir-lhe sem custos adicionais. Ao selecionar uma configuração redundante de zona, pode tornar as suas bases de dados Premium ou Business Critical resilientes a um conjunto muito maior de falhas, incluindo falhas catastróficas de datacenter, sem alterações na lógica da aplicação. Também pode converter quaisquer bases de dados ou piscinas Premium ou Business Critical existentes para a configuração redundante da zona.

Como as bases de dados redundantes da zona têm réplicas em diferentes datacenters com alguma distância entre eles, o aumento da latência da rede pode aumentar o tempo de compromisso e, assim, impactar o desempenho de algumas cargas de trabalho oLTP. Pode sempre voltar à configuração de zona única, desativando a definição de redundância da zona. Este processo é uma operação online semelhante à atualização regular do nível de serviço. No final do processo, a base de dados ou piscina é migrada de um anel redundante de zona para um único anel de zona ou vice-versa.

> [!IMPORTANT]
> Atualmente, as bases de dados redundantes da zona e as piscinas elásticas são suportadas apenas nos níveis de serviço Premium e Business Critical em regiões selecionadas. Ao utilizar o nível Business Critical, a configuração redundante da zona só está disponível quando o hardware computacional Gen5 é selecionado. Para informações atualizadas sobre as regiões que suportam bases de dados redundantes da zona, consulte [o suporte de serviços por região.](../availability-zones/az-overview.md#services-support-by-region)  
> Esta funcionalidade não está disponível em instância gerida.

A versão redundante da zona da arquitetura de alta disponibilidade é ilustrada pelo seguinte diagrama:

![zona de arquitetura de alta disponibilidade redundante](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Recuperação acelerada da base de dados (ADR)

[Accelerated Database Recovery (ADR)](sql-database-accelerated-database-recovery.md) é uma nova funcionalidade do motor de base de dados SQL que melhora consideravelmente a disponibilidade de bases de dados, especialmente na presença de transações de longa duração. ATualmente, o ADR está disponível para bases de dados individuais, piscinas elásticas e Armazém de Dados Azure SQL.

## <a name="testing-application-fault-resiliency"></a>Resistência à falha da aplicação de teste

A elevada disponibilidade é uma parte fundamental da plataforma Azure SQL Database que funciona de forma transparente para a sua aplicação de base de dados. No entanto, reconhecemos que pode querer testar como as operações automáticas de failover iniciadas durante eventos planeados ou não planeados teriam impacto na aplicação antes de a implementar em produção. Pode chamar uma API especial para reiniciar uma base de dados ou uma piscina elástica, o que por sua vez irá desencadear uma falha. No caso de uma base de dados redundante ou de um pool elástico, a chamada da API resultaria na redirecionamento das ligações dos clientes para a nova primária numa Zona de Disponibilidade diferente da Zona de Disponibilidade das antigas primárias. Assim, para além de testar como o falha afeta as sessões de base de dados existentes, também pode verificar se altera o desempenho de ponta a ponta devido a alterações na latência da rede. Como a operação de reinício é intrusiva e um grande número deles pode realçar a plataforma, apenas uma chamada failover é permitida a cada 30 minutos para cada base de dados ou piscina elástica. 

Uma falha pode ser iniciada usando REST API ou PowerShell. Para a Rest API, consulte a [falha da base de dados](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) e a falha da piscina [elástica](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover). Para powerShell, consulte [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) e [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). As chamadas REST API também podem ser feitas a partir de Azure CLI usando o comando [de repouso az.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest)

> [!IMPORTANT]
> O comando Failover não está atualmente disponível no nível de serviço de Hiperescala e em Caso Gerido.

## <a name="conclusion"></a>Conclusão

A Base de Dados Azure SQL apresenta uma solução incorporada de alta disponibilidade, profundamente integrada com a plataforma Azure. Está dependente do Tecido de Serviço para deteção e recuperação de falhas, no armazenamento de Azure Blob para proteção de dados, e em Zonas de Disponibilidade para maior tolerância a falhas. Além disso, a base de dados Azure SQL aproveita a tecnologia do Grupo Always On Availability do SQL Server para replicação e falha. A combinação destas tecnologias permite que as aplicações realizem plenamente os benefícios de um modelo de armazenamento misto e suportem os SLAs mais exigentes.

## <a name="next-steps"></a>Passos seguintes

- Conheça [as Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre [o Tecido de Serviço](../service-fabric/service-fabric-overview.md)
- Conheça o [Gestor de Tráfego Azure](../traffic-manager/traffic-manager-overview.md)
- Para mais opções para alta disponibilidade e recuperação de desastres, consulte [Continuidade do Negócio](sql-database-business-continuity.md)

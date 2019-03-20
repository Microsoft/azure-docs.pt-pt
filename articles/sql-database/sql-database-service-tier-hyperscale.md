---
title: Descrição geral de Hiperescala de base de dados SQL do Azure | Documentos da Microsoft
description: Este artigo descreve a camada de serviços de Hiperescala o modelo de compra baseado em vCore na base de dados do Azure SQL e explica como é diferente dos escalões de serviço para fins gerais e crítico para a empresa.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 4db013b51657bb327c36d616a3743c46731cd19a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903798"
---
# <a name="hyperscale-service-tier-preview-for-up-to-100-tb"></a>Camada de serviços de Hiperescala (pré-visualização) para até 100 TB

Base de dados SQL do Azure baseia-se na arquitetura de motor de base de dados do SQL Server que é ajustada para o ambiente de cloud para garantir a disponibilidade de 99,99% mesmo em caso de falhas de infraestrutura. Existem três modelos de arquiteturais que são utilizados na base de dados do Azure SQL:

- Geral finalidade/Standard 
- Críticos de negócios/Premium
- Hiperescala

A camada de serviços de Hiperescala na base de dados do Azure SQL é a camada de serviço mais recente no modelo de compra baseado em vCore. Este escalão de serviço é um armazenamento de aplicações altamente escalável e o escalão de desempenho de computação que tira partido da arquitetura do Azure para aumentar horizontalmente o armazenamento e os recursos de computação para uma base de dados do SQL do Azure substancialmente além dos limites disponíveis para fins gerais e de negócios Escalões de serviço crítico.

> [!IMPORTANT]
> Camada de serviços de Hiperescala está atualmente em pré-visualização pública e disponível em regiões do Azure limitados. Para obter a lista completa de região, veja [regiões disponíveis da camada de serviços de Hiperescala](#available-regions). Não recomendamos a execução de qualquer carga de trabalho de produção nos bancos de dados de Hiperescala ainda. Não é possível atualizar uma base de dados de Hiperescala para outros escalões de serviço. Para fins de teste, recomendamos que faça uma cópia da base de dados atual e atualizar a cópia para a camada de serviços de Hiperescala.
> [!NOTE]
> Para obter detalhes sobre os escalões de serviço para fins gerais e crítico para a empresa no modelo de compra baseado em vCore, consulte [fins gerais](sql-database-service-tier-general-purpose.md) e [críticas para a empresa](sql-database-service-tier-business-critical.md) escalões de serviço. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [compra de modelos e recursos do Azure SQL Database](sql-database-purchase-models.md).
> [!IMPORTANT]
> Camada de serviços de Hiperescala está atualmente em pré-visualização pública. Não recomendamos a execução de qualquer carga de trabalho de produção nos bancos de dados de Hiperescala ainda. Não é possível atualizar uma base de dados de Hiperescala para outros escalões de serviço. Para fins de teste, recomendamos que faça uma cópia da base de dados atual e atualizar a cópia para a camada de serviços de Hiperescala.

## <a name="what-are-the-hyperscale-capabilities"></a>Quais são os recursos de Hiperescala

A camada de serviços de Hiperescala na base de dados do Azure SQL fornece as seguintes capacidades adicionais:

- Suporte para até 100 TB de tamanho de base de dados
- Quase instantânea (baseadas em instantâneos de ficheiros armazenados no armazenamento de Blobs do Azure) de cópias de segurança de base de dados, independentemente do tamanho, sem qualquer impacto na e/s de computação   
- Rápido restaurações de base de dados (baseadas em instantâneos de ficheiros) em minutos em vez de horas ou dias (não um tamanho de operação de dados)
- Maior desempenho geral devido a maior débito de registo e tempos mais rápidos de consolidação de transação, independentemente de volumes de dados
- Rápido aumentar horizontalmente – pode aprovisionar nós só de leitura um ou mais para o descarregamento de sua carga de trabalho de leitura e para utilização como servidores de reserva quente
- Rápido aumento vertical - pode, em constante tempo, aumentar verticalmente os recursos de computação para acomodar cargas de trabalho pesadas, como e quando for necessárias e, em seguida, dimensionar os recursos de computação voltar a reduzir quando não for necessário.

A camada de serviços de Hiperescala remove muitos dos limites práticos tradicionalmente vistos em bases de dados na cloud. Em que o mais outros bancos de dados estão limitados pelos recursos disponíveis num único nó, bancos de dados na camada de serviços de Hiperescala têm sem esses limites. Com sua arquitetura de armazenamento flexíveis, o armazenamento aumenta conforme necessário. Na verdade, as bases de dados de grande dimensão não são criados com um tamanho máximo definido. Uma base de dados de Hiperescala aumenta conforme necessário – e é faturado apenas pela capacidade que utiliza. Para cargas de trabalho de leitura intensiva, a camada de serviços de Hiperescala fornece o Escalamento horizontal rápido através do aprovisionamento de réplicas de leitura adicionais conforme necessário para descarregar cargas de trabalho de leitura.

Além disso, o tempo necessário para criar cópias de segurança da base de dados ou para aumentar verticalmente ou para baixo, já não está ligado ao volume de dados na base de dados. Bases de dados de grande dimensão podem ser uma cópia de segurança praticamente instantaneamente. Pode também dimensionar uma base de dados de dezenas de terabytes ou reduzir verticalmente em minutos. Esta capacidade permite que preocupações sobre a ser demarcado por suas opções de configuração inicial.

Para obter mais informações sobre os tamanhos de computação para a camada de serviços de Hiperescala, consulte [características de camada de serviço](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Quem deve considerar a camada de serviços de Hiperescala

A escalão de serviço destina-se principalmente para clientes com grandes bancos de dados de Hiperescala seja no local e pretender precisem de modernizar os seus aplicativos, movendo para a nuvem ou para clientes que já estão na cloud e são limitados pelo tamanho da base de dados máximo restrições (1 a 4 TB). Destina-se também para clientes que procuram de alto desempenho e alta escalabilidade para o armazenamento e computação.

A camada de serviços de Hiperescala suporta todas as cargas de trabalho do SQL Server, mas principalmente está otimizado para OLTP. A camada de serviços de Hiperescala também oferece suporte híbrido e analíticos cargas de trabalho (datamart).

> [!IMPORTANT]
> Conjuntos elásticos não suportam a camada de serviços de Hiperescala.

## <a name="hyperscale-pricing-model"></a>Modelo de preços de Hiperescala

Só está disponível na camada de serviços de Hiperescala [modelo de vCore](sql-database-service-tiers-vcore.md). Para alinhar com a nova arquitetura, o modelo de preços é ligeiramente diferente do escalões de serviço crítico para a empresa ou de fins gerais:

- **Computação**:

  O preço de unidade de computação de grande escala é por réplica. O [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) preço é aplicado automaticamente a ler as réplicas de dimensionamento. Em pré-visualização pública, criamos duas réplicas por base de dados de grande dimensão por predefinição.

- **Armazenamento**:

  Não precisa de especificar o tamanho de dados máximo quando configurar uma base de dados de grande escala. No nível de hiperescala, é-lhe cobrado o armazenamento da sua base de dados com base na utilização real. O armazenamento é alocado de forma dinâmica entre 5 GB e 100 TB, em incrementos de 1 GB.  

Para obter mais informações sobre os preços de Hiperescala, consulte [preços de base de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arquitetura de funções distribuídas

Ao contrário dos motores de bases de dados tradicionais centralizou a todas as funções de gestão de dados num local/processo (até mesmo, de modo chamados bases de dados distribuídas em produção hoje tem múltiplas cópias de um mecanismo de dados monolítica), uma base de dados de Hiperescala separa o motor de processamento de consultas, em que divergem a semântica dos vários mecanismos de dados, dos componentes que fornecem armazenamento a longo prazo e durabilidade dos dados. Dessa forma, a capacidade de armazenamento pode ser facilmente ampliada até onde conforme necessário (destino inicial é de 100 TB). Réplicas só de leitura partilham os mesmos componentes de computação para que nenhuma cópia de dados é necessário para criar uma nova réplica legível. Durante a pré-visualização, é suportada apenas 1 réplica só de leitura.

O diagrama seguinte ilustra os diferentes tipos de nós numa Hiperescala base de dados:

![arquitetura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Uma base de dados de grande dimensão contém os seguintes tipos diferentes de nós:

### <a name="compute-node"></a>Nó de computação

O nó de computação é onde reside o mecanismo relacional, para que todos os elementos de linguagem, processamento de consultas e assim por diante, ocorrem. Nós de computação de todas as interações de utilizador com uma base de dados de Hiperescala acontecer por elas. Computação de nós ter caches baseado em SSD (rotulado como RBPEX - extensão do conjunto de memória intermédia Resiliente no diagrama anterior), para minimizar o número de rede IDA e volta necessárias para obter uma página de dados. Existe um nó de computação principal em que todas as cargas de trabalho de leitura / escrita e transações são processadas. Existem um ou mais nós de computação secundário que atuam como acesso frequente nós em modo de espera para fins de ativação pós-falha, bem como a agirem como nós de computação de só de leitura para o descarregamento de cargas de trabalho de leitura (se essa funcionalidade for o pretendido).

### <a name="page-server-node"></a>Nó de servidor de página

Servidores de página são sistemas que representa um mecanismo de armazenamento de escalamento horizontal.  Cada servidor de página é responsável por um subconjunto das páginas na base de dados.  Nominally, cada servidor de página controla 1 terabyte de dados. Não existem dados são partilhados em mais de um servidor de página (fora de réplicas que são mantidos para redundância e disponibilidade). A tarefa de um servidor de página é servir páginas de base de dados para os nós de computação a pedido e para manter as páginas atualizadas como dados de atualização de transações. Servidores de página são mantidas atualizadas com a reprodução de registros de log do serviço de registo. Servidores de página também mantenham caches baseado em SSD para aprimorar o desempenho. Armazenamento de longa duração de páginas de dados é mantido no armazenamento do Azure para oferecer confiabilidade adicional.

### <a name="log-service-node"></a>Nó de serviço de registo

O nó do serviço de registo aceita registros de log do nó de computação principal, persiste-los num cache durável e reencaminha os registros de log para o restante de nós de computação (para que possam atualizar seus caches), bem como o servidor de página relevante (es), para que os dados podem ser atualizado t aqui. Dessa forma, todas as alterações de dados do nó de computação primária são propagadas através do serviço de registo para todos os nós de computação secundário e os servidores da página. Por fim, os registos de log são instalados sem solicitação para o armazenamento de longo prazo no armazenamento do Azure, o que é um repositório de armazenamento infinito. Esse mecanismo remove a necessidade de truncagem de registos frequentes. O serviço de registo também tem um cache local para acelerar o acesso.

### <a name="azure-storage-node"></a>Nó de armazenamento do Azure

O nó de armazenamento do Azure é o destino final de dados de servidores de página. Este armazenamento é utilizado para fins de backup, bem como para a replicação entre regiões do Azure. As cópias de segurança consistem em instantâneos de ficheiros de dados. Restaurar a operação são rápidos destes instantâneos e dados podem ser restaurados para qualquer ponto no tempo.

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

As cópias de segurança são instantâneos de ficheiros de base e, por conseguinte, são quase instantâneas. Separação de armazenamento e computação ativar enviar a operação de cópia de segurança/restauro para a camada de armazenamento reduzir a carga de processamento no nó de computação principal. Como resultado, a cópia de segurança de um banco de dados não afeta o desempenho do nó de computação principal. Da mesma forma, restaurações são feitas ao copiar o instantâneo de ficheiro como tal, não sendo um tamanho de operação de dados. Para restauros dentro da mesma conta de armazenamento, a operação de restauro é rápida.

## <a name="scale-and-performance-advantages"></a>Vantagens de dimensionamento e desempenho

Com a capacidade de rapidamente acelerar/desacelerar nós de computação adicionais de só de leitura, a arquitetura permite significativa de Hiperescala capacidades de escala de leitura e também pode libertar o nó de computação principal para satisfazer as necessidades mais pedidos de escrita. Além disso, os nós de computação podem ser dimensionados para cima/para baixo rapidamente devido à arquitetura de armazenamento partilhado da arquitetura de Hiperescala.

## <a name="create-a-hyperscale-database"></a>Criar uma base de dados de grande escala

Uma base de dados de grande escala pode ser criada com o [portal do Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase) ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Bases de dados de grande dimensão estão disponíveis apenas usando o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

O seguinte comando do T-SQL cria uma base de dados de grande escala. Tem de especificar o objetivo do edition e o serviço no `CREATE DATABASE` instrução.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrar uma base de dados de SQL do Azure existente para a camada de serviços de Hiperescala

Pode mover as bases de dados SQL do Azure existentes para a utilização de Hiperescala a [portal do Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Em pré-visualização pública, esta é uma migração unidirecional. Não é possível mover bases de dados de Hiperescala para outra camada de serviço. Recomendamos que faça uma cópia dos seus bancos de dados de produção e migrar para o de Hiperescala para uma prova de conceitos (provas de conceito).

O comando T-SQL seguinte move uma base de dados para a camada de serviços de Hiperescala. Tem de especificar o objetivo do edition e o serviço no `ALTER DATABASE` instrução.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Ligar a uma réplica de uma escala de leitura de uma base de dados de grande escala

Em bancos de dados de grande escala, o `ApplicationIntent` argumento na cadeia de ligação fornecido pelo cliente determina se a ligação é encaminhada para a réplica de escrita ou para uma réplica secundária só de leitura. Se o `ApplicationIntent` definido como `READONLY` e a base de dados não tem uma réplica secundária, a ligação será encaminhada para a réplica primária e a predefinição é `ReadWrite` comportamento.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="available-regions"></a>Regiões disponíveis

Camada de serviços de Hiperescala está atualmente em pré-visualização pública e disponível nas seguintes regiões do Azure: E.U.A. leste, 1, E.U.A. Leste 2, E.u.a. oeste2, E.U.A. Central, Norte CentralU S, Europa Ocidental, Europa do Norte, leste da Austrália, Sudeste da Austrália, Sudeste asiático, leste do Japão e Coreia Central

## <a name="known-limitations"></a>Limitações conhecidas

| Problema | Descrição |
| :---- | :--------- |
| O painel de gerir cópias de segurança de uma base de dados do SQL server não mostra os bancos de dados de grande dimensão serão filtrados do SQL server ->  | Hiperescala tem um método separado para a gestão de cópias de segurança, e assim a retenção de longo prazo e o ponto em definições de retenção de cópia de segurança de tempo não se aplicam / são também invalidados. Da mesma forma, bases de dados de Hiperescala não aparecem no painel de gerir a cópia de segurança. |
| Restauro para um ponto anterior no tempo | Depois de uma base de dados é migrado para a camada de serviços de Hiperescala, restauro para um ponto anterior no tempo antes da migração não é suportado.|
| Se um ficheiro de base de dados aumenta durante a migração devido a uma carga de trabalho do Active Directory e ultrapassar 1 TB por limite de ficheiro, a migração falhará | Atenuações: <br> -Se possível, migre a base de dados quando não existe nenhuma carga de trabalho de atualização em execução.<br> -Tente novamente a migração, será bem sucedida, desde que o limite de 1 TB não é cruzado durante a migração.|
| Instância gerida não é atualmente suportada | Não são atualmente suportados |
| A migração para Hiperescala atualmente é uma operação unidirecional | Depois de uma base de dados é migrada a Hiperescala, não é possível migrar diretamente para um escalão de serviço não Hiperescala. No momento, a única forma de migrar uma base de dados de Hiperescala para não Hiperescala é exportação/importação através de um ficheiro BACPAC.|
| Migração de bases de dados com objetos em memória não é atualmente suportada | Objetos em memória tem de ser removidos e recriados como objetos não na memória antes de migrar uma base de dados para a camada de serviços de Hiperescala.|
| Atualmente, o controlo de dados de alterações não é suportada. | Não será capaz de usar o controlo de dados de alterações com databasess de Hiperescala.

## <a name="next-steps"></a>Passos Seguintes

- Para uma FAQ sobre a Hiperescala, consulte [perguntas freqüentes sobre Hiperescala](sql-database-service-tier-hyperscale-faq.md).
- Para obter informações sobre escalões de serviço, consulte [escalões de serviço](sql-database-purchase-models.md)
- Ver [limita a visão geral dos recursos num servidor de base de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e de subscrição.
- Para a compra de limites de modelo para uma base de dados, consulte [base de dados do SQL Azure limites de modelo para uma base de dados de compra baseado em vCore](sql-database-vcore-resource-limits-single-databases.md).
- Para funcionalidades e lista de comparação, veja [recursos comuns de SQL](sql-database-features.md).

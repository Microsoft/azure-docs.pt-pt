---
title: Gerir bases de dados de cloud aumentadas horizontalmente | Documentos da Microsoft
description: Utilize o serviço de tarefa de bases de dados elásticas para executar um script longo de um grupo de bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 8abb2e3ac4f62a3ea51cc686bbf23260fccc4077
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441377"
---
# <a name="managing-scaled-out-cloud-databases"></a>Gerir bases de dados de cloud escaladas horizontalmente

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]

**Tarefas de base de dados elásticas** é um hospedado pelo cliente do Azure serviço Cloud que permite a execução das tarefas administrativas ad hoc e agendadas, que são chamados **tarefas**. Com tarefas, pode facilmente e gerir com fiabilidade grandes grupos de bases de dados SQL do Azure através da execução de scripts do Transact-SQL para realizar operações administrativas.

Para gerir bases de dados em partição horizontal de escalamento horizontal, o **tarefas de bases de dados elásticas** funcionalidade (pré-visualização) permite-lhe uma forma fiável, executar um script de Transact-SQL (T-SQL) num grupo de bases de dados, incluindo:

- um conjunto personalizado de bases de dados (explicado abaixo)
- todas as bases de dados num [conjunto elástico](sql-database-elastic-pool.md)
- um conjunto de partições horizontais (criado usando [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-database-client-library.md)).

Para obter mais informações sobre o conceito de uma base de dados em partição horizontal, consulte [fragmentação uma base de dados do SQL Server](https://blog.pythian.com/sharding-sql-server-database/).

## <a name="documentation"></a>Documentação

- [Instalar os componentes de trabalho de bases de dados elásticas](sql-database-elastic-jobs-service-installation.md).
- [Introdução às tarefas de bases de dados elásticas](sql-database-elastic-jobs-getting-started.md).
- [Criar e gerir tarefas através do PowerShell](sql-database-elastic-jobs-powershell.md).
- [Criar e gerir aumentados horizontalmente de bases de dados SQL do Azure](sql-database-elastic-jobs-getting-started.md)

![Serviço de tarefa de bases de dados elásticas][1]

## <a name="why-use-jobs"></a>Por que usar tarefas

### <a name="manage"></a>Gerir

Facilmente fazer alterações de esquema, gestão de credenciais, atualizações de dados de referência, recolha de dados de desempenho ou a coleção de telemetria do inquilino (cliente).

### <a name="reports"></a>Relatórios

Agregue dados a partir de uma coleção de bases de dados SQL do Azure numa tabela de destino única.

### <a name="reduce-overhead"></a>Reduzir o overhead

Normalmente, tem de ligar a cada base de dados de forma independente para executar instruções Transact-SQL ou para realizar outras tarefas administrativas. Uma tarefa processa a tarefa de início de sessão em cada base de dados no grupo de destino. Também pode definir, manter e persistir os scripts Transact-SQL para serem executados num grupo de bases de dados SQL do Azure.

### <a name="accounting"></a>Contabilidade

Tarefas de executam o script e registar o estado de execução para cada base de dados. Também obtém a repetição automática quando ocorrem falhas.

### <a name="flexibility"></a>Flexibilidade

Defina grupos personalizados de bases de dados SQL do Azure e defina agendamentos para executar uma tarefa.

> [!NOTE]
> No portal do Azure, apenas um conjunto reduzido de funções limitadas para conjuntos elásticos do SQL Azure está disponível. Utilize as APIs do PowerShell para aceder ao conjunto completo de funcionalidade atual.

## <a name="applications"></a>Aplicações

- Efetue tarefas administrativas, tais como implementar um novo esquema.
- Atualize informações de produto de dados de referência comuns em todas as bases de dados. Ou agendas automático atualiza cada dia da semana, após horas.
- Reconstrua índices para melhorar o desempenho das consultas. A reconstrução pode ser configurada para executar numa coleção de bases de dados de forma recorrente, tal como horário de pico.
- Recolha os resultados da consulta a partir de um conjunto de bases de dados numa tabela central de forma contínua. As consultas de desempenho podem ser continuamente executadas e configuradas para acionar tarefas adicionais para serem executadas.
- Execute consultas de processamento de dados de execução mais longa num grande conjunto de bases de dados, por exemplo, a coleção de telemetria de cliente. Os resultados são recolhidos para uma tabela de destino única para análise adicional.

## <a name="elastic-database-jobs-end-to-end"></a>Tarefas elásticas da base de dados:-a-ponto

1. Instalar o **tarefas de bases de dados elásticas** componentes. Para obter mais informações, consulte [tarefas de instalação de bases de dados elásticas](sql-database-elastic-jobs-service-installation.md). Se a instalação falhar, veja [como desinstalar](sql-database-elastic-jobs-uninstall.md).
2. Utilize as APIs do PowerShell para aceder a mais funcionalidade, por exemplo, criar coleções de base de dados personalizado, a adição de agendas de e/ou conjuntos de resultados de recolha. Utilizar o portal para instalação simple e criação/monitorização de tarefas limitado para execução em relação a um **conjunto elástico**.
3. Criar credenciais encriptadas para a execução da tarefa e [adicione o utilizador (ou a função) para cada base de dados no grupo de](sql-database-security-overview.md).
4. Crie um script T-SQL que pode ser executada para cada base de dados no grupo de idempotentes.
5. Siga estes passos para criar tarefas no portal do Azure: [Criar e gerir tarefas de bases de dados elásticas](sql-database-elastic-jobs-create-and-manage.md).
6. Ou utilizar scripts do PowerShell: [Criar e gerir um tarefas de base de dados elástica de base de dados SQL com o PowerShell (pré-visualização)](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Scripts Idempotent

Os scripts tem de ser [idempotentes](https://en.wikipedia.org/wiki/Idempotence). Em termos simples, "idempotente" significa que, se o script tiver êxito, e ele é executado novamente, o mesmo resultado ocorre. Um script pode falhar devido a problemas de rede transitória. Nesse caso, a tarefa repetirá automaticamente a execução do script um número predefinido de vezes antes de desistir. Um script de idempotentes tem o mesmo resultado, mesmo que tenha sido executada com êxito duas vezes.

Uma tática simples consiste em testar a existência de um objeto antes de o criar.  

```sql
    IF NOT EXIST (some_object)
    -- Create the object
```

Da mesma forma, um script tem de poder ser executado com êxito ao testar logicamente e refutar as condições que encontrar.

## <a name="failures-and-logs"></a>Falhas e registos

Se um script falhar após várias tentativas, a tarefa regista o erro e continua. Após o final de uma tarefa (ou seja, uma execução em todas as bases de dados no grupo), pode verificar sua lista de tentativas falhadas. Os registos de fornecer detalhes depurar scripts com falhas.

## <a name="group-types-and-creation"></a>Tipos de grupos e a criação

Existem dois tipos de grupos:

1. Conjuntos de partição horizontal
2. Grupos personalizados

Grupos de conjunto de partições horizontais são criados com o [ferramentas de bases de dados elásticas](sql-database-elastic-scale-introduction.md). Quando cria um grupo de conjunto de partições horizontais, bases de dados são adicionados ou removidos do grupo automaticamente. Por exemplo, uma nova partição horizontal será automaticamente no grupo de quando adicioná-lo para o mapa de partições horizontais. Uma tarefa, em seguida, pode ser executada em relação ao grupo.

Grupos personalizados, por outro lado, rigidamente são definidos. Tem explicitamente de adicionar ou remover bases de dados de grupos personalizados. Se uma base de dados no grupo for removido, a tarefa irá tentar executar o script na base de dados, resultando numa eventual falha. Grupos criados no portal do Azure atualmente são grupos personalizados.

## <a name="components-and-pricing"></a>Preços e de componentes

Os seguintes componentes funcionam em conjunto para criar um serviço de nuvem do Azure que permite a execução ad hoc das tarefas administrativas. Os componentes instalados e configurados automaticamente durante a configuração, na sua subscrição. É possível identificar os serviços, como todos eles têm o mesmo nome gerado automaticamente. O nome é exclusivo e consiste no prefixo "edj" seguido de 21 carateres gerados aleatoriamente.

- Serviço Cloud do Azure

  Tarefas de base de dados elástica (pré-visualização) é entregue como um serviço de nuvem do Azure hospedado pelo cliente para efetuar a execução das tarefas pedidas. No portal, o serviço é implementado e alojado na sua subscrição do Microsoft Azure. A predefinição implementado execuções de serviço com o mínimo de duas funções de trabalho para elevada disponibilidade. O tamanho predefinido de cada função de trabalho (ElasticDatabaseJobWorker) é executado numa instância de A0. Para preços, veja [preços de serviços Cloud](https://azure.microsoft.com/pricing/details/cloud-services/).

- Base de Dados SQL do Azure

  O serviço utiliza uma base de dados de SQL do Azure conhecido como o **base de dados do controle** para armazenar todos os metadados de tarefa. A camada de serviço predefinida é um S0. Para preços, veja [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

- Service Bus do Azure

  Um barramento de serviço do Azure destina-se a coordenação do trabalho no serviço Cloud do Azure. Ver [preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

- Storage do Azure

  Uma conta de armazenamento do Azure é utilizada para armazenar a saída de diagnóstico de registo no caso de um problema requer uma depuração ainda mais (consulte [ativar diagnósticos nos serviços Cloud do Azure e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md)). Para preços, veja [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Como funcionam as tarefas de bases de dados elásticas

1. Uma base de dados do SQL do Azure é designado uma **base de dados do controle** que armazena todos os dados de metadados e o estado.
2. A base de dados do controle é acessada pela **serviço de tarefas** para iniciar e controlar tarefas para executar.
3. Duas funções diferentes para comunicam com a base de dados de controle:
   - Controlador: Determina quais tarefas exigem tarefas a executar a tarefa pedida e tarefas com falhas de repetições, criando novas tarefas de trabalho.
   - Execução de tarefas da tarefa: Realiza as tarefas de trabalho.

### <a name="job-task-types"></a>Tipos de tarefas da tarefa

Existem vários tipos de tarefas que desempenha a execução de tarefas:

- ShardMapRefresh

  Consulta o mapa de partições horizontais para determinar todas as bases de dados usados como partições horizontais
- ScriptSplit

  Divide o script em instruções de "GO" em lotes
- ExpandJob

  Cria tarefas de cada base de dados de subordinado a partir de uma tarefa que tenha como destino um grupo de bases de dados
- ScriptExecution

  Executa um script em relação a um determinado banco de dados com as credenciais definidas
- Dacpac

  Aplica-se um DACPAC a uma determinada base de dados com as credenciais específicas

## <a name="end-to-end-job-execution-work-flow"></a>Fluxo de trabalho da execução a tarefa de ponto a ponto

1. Utilizar o Portal ou a API do PowerShell, uma tarefa é inserida o **base de dados do controle**. A execução de pedidos de tarefa de um script de Transact-SQL em relação a um grupo de bases de dados usando credenciais específicas.
2. O controlador identifica a nova tarefa. Tarefas de trabalho são criadas e executadas para dividir o script e atualizar bancos de dados do grupo. Por último, uma nova tarefa é criada e executada para expandir a tarefa e criar novo item subordinado em que cada tarefa subordinada é especificada para executar o script de Transact-SQL em relação a uma base de dados individual no grupo de tarefas.
3. O controlador identifica as tarefas filho criado. Para cada tarefa, o controlador cria e aciona uma tarefa para executar o script num banco de dados.
4. Depois de concluíram todas as tarefas de trabalho, o controlador atualiza os trabalhos para um estado concluído.
   Em qualquer momento durante a execução de tarefa, pode servir-se a API do PowerShell para ver o estado atual de execução da tarefa. Todos os momentos devolvidos pelas APIs do PowerShell são representados em UTC. Se assim o desejar, uma solicitação de cancelamento pode ser iniciada para parar uma tarefa.

## <a name="next-steps"></a>Passos Seguintes

[Instalar os componentes](sql-database-elastic-jobs-service-installation.md), em seguida, [criar e adicionar um início de sessão para cada base de dados no grupo de bases de dados](sql-database-manage-logins.md). Para compreender melhor a criação da tarefa e de gestão, consulte [criar e gerir tarefas de bases de dados elásticas](sql-database-elastic-jobs-create-and-manage.md). Consulte também [introdução às tarefas de bases de dados elásticas](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

---
title: Base de dados SQL sem servidor (pré-visualização do Azure) | Documentos da Microsoft
description: Este artigo descreve o novo escalão de computação sem servidor e o compara com o escalão de computação aprovisionada existente
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/05/2019
ms.openlocfilehash: 5a1a5ea39c9c0ed8973e1ecfa46977d2d06f83e7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603614"
---
# <a name="azure-sql-database-serverless-preview"></a>SQL Database do Azure sem servidor (pré-visualização)

Base de dados do SQL do Azure (pré-visualização) sem servidor é um escalão de computação para bases de dados individuais que dimensiona-se automaticamente com base na demanda de carga de trabalho e listas para a quantidade de computação de computação utilizada por segundo. O escalão de computação sem servidor também automaticamente em pausa, bases de dados durante períodos Inativos quando o armazenamento apenas é faturado e retoma automaticamente as bases de dados quando a atividade devolve.

## <a name="serverless-compute-tier"></a>Escalão de serviço de computação sem servidor

A camada de computação sem servidor para uma base de dados é parametrizada por um intervalo de dimensionamento automático de computação e um atraso de autopause.  A configuração destes parâmetros moldar a experiência de desempenho da base de dados e custo de computação.

![Faturação sem servidor](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Configuração de desempenho

- O **vCores mínima** e **vCores máximo** são parâmetros configuráveis que definem o intervalo de capacidade de computação disponível para a base de dados. Limites de memória e e/s são proporcionais ao intervalo de vCore especificado.  
- O **atraso autopause** é um parâmetro configurável que define o período de tempo da base de dados tem de estar inativo antes de ele automaticamente é colocada em pausa. A base de dados é retomada automaticamente quando ocorre o próximo início de sessão ou de outra atividade.  Em alternativa, pode ser desativado autopausing.

### <a name="cost"></a>Custo

- O custo de uma base de dados sem servidor é a soma do custo de computação e o custo de armazenamento.
- Quando a utilização de computação é entre o mínimo e os limites máximos configurados, o custo de computação é baseado em vCore e de memória utilizada.
- Quando a utilização de computação é abaixo os limites mínimo configurados, o custo de computação baseia-se na min vCores e memória mínima configurada.
- Quando a base de dados está em pausa, o custo de computação é zero e apenas os custos de armazenamento são cobrados.
- O custo de armazenamento é determinado da mesma forma como o escalão de computação aprovisionados.

Para obter mais detalhes de custo, veja [faturação](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Cenários

Sem servidor é o preço-desempenho otimizada para bases de dados individuais com padrões de utilização imprevisíveis, intermitente conseguir sustentar algum atraso na computação aquecimento após períodos de inatividade de utilização. Por outro lado, o escalão de computação aprovisionada é relação preço-desempenho otimizada para bases de dados únicas ou várias bases de dados nos conjuntos elásticos com maior utilização média que não pode estar eventuais atrasos na computação aquecimento.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Cenários adequados para a computação sem servidor

- Bases de dados individuais com padrões de utilização imprevisíveis, intermitentes intercaladas com períodos de inatividade e mais baixa utilização de computação média ao longo do tempo.
- Bases de dados individuais no escalão de computação aprovisionada que freqüentemente são redimensionado e os clientes que preferem delegar de computação ao redimensionar o para o serviço.
- Novas bases de dados individuais sem histórico de utilização em que o dimensionamento de computação é difícil ou não é possível fazer uma estimativa antes da implantação na base de dados SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Cenários adequados para computação aprovisionada

- Bases de dados individuais com padrões de utilização mais regular e previsível e a maior média de utilização ao longo do tempo de computação.
- Bases de dados que não toleram vantagens e desvantagens de desempenho resultantes de mais frequentes memória corte ou atraso em autoresuming a partir de um Estado em pausa.
- Várias bases de dados com padrões de utilização imprevisíveis, intermitentes que podem ser consolidados em conjuntos elásticos para uma melhor otimização de desempenho por preço.

## <a name="comparison-with-provisioned-compute-tier"></a>Comparação com o escalão de computação aprovisionada

A tabela seguinte resume as diferenças entre a camada de computação sem servidor e o escalão de computação aprovisionada:

| | **Computação sem servidor** | **Computação aprovisionada** |
|:---|:---|:---|
|**Padrão de utilização da base de dados**| Utilização imprevisível, intermitente com mais baixa utilização de computação média ao longo do tempo. |  Padrões de utilização mais regulares com a maior média de utilização ao longo do tempo, ou várias bases de dados utilizam conjuntos elásticos de computação.|
| **Esforço de gestão de desempenho** |Inferior|Superior|
|**Dimensionamento de computação**|Automático|Manual|
|**Capacidade de resposta de computação**|Inferior após períodos Inativos|Imediata|
|**Granularidade de faturação**|Por segundo|Por hora|

## <a name="purchasing-model-and-service-tier"></a>Escalão de serviço e modelo de compra

Sem servidor da base de dados SQL é atualmente suportada apenas no escalão fins gerais em hardware de geração 5 no vCore modelo de compra.

## <a name="autoscaling"></a>Dimensionamento automático

### <a name="scaling-responsiveness"></a>Dimensionar a capacidade de resposta

Em geral, os bancos de dados sem servidor são executados numa máquina com capacidade suficiente para satisfazer a procura de recursos sem interrupção para qualquer quantidade de computação a pedido dentro dos limites definidos pelo valor máximo de vCores. Ocasionalmente, automaticamente balanceamento de carga ocorre se a máquina não consegue satisfazer a procura de recursos dentro de alguns minutos. Por exemplo, se a demanda de recursos é 4 vCores, mas apenas 2 vCores estão disponíveis, em seguida, ele poderá demorar até a alguns minutos para balanceamento de carga, antes de 4 vCores são fornecidos. A base de dados permanece online durante o balanceamento de carga, exceto para um breve período no final da operação quando as ligações são ignoradas.

### <a name="memory-management"></a>Gerenciamento de memória

Memória para bases de dados sem servidor é recuperada mais frequentemente do que para bases de dados de computação aprovisionados. Este comportamento é importante para controlar os custos em sem servidor e pode afetar o desempenho.

#### <a name="cache-reclamation"></a>Recuperação de cache

Ao contrário das bases de dados de computação aprovisionada, memória do cache SQL é recuperada de um banco de dados sem servidor quando a utilização de CPU ou de cache é baixa.

- Utilização da cache é considerada baixa quando o tamanho total das mais utilizada recentemente entradas de cache cai abaixo de um limite para um período de tempo.
- Quando a recuperação de cache é disparada, o tamanho da cache de destino é reduzido de forma incremental a uma fração do tamanho anterior e apenas reclamação continua se utilização permanece baixa.
- Quando ocorre a recuperação de cache, a política para a seleção de entradas de cache para expulsar é a mesma política de seleção como para bases de dados de computação aprovisionada quando a pressão de memória é elevada.
- O tamanho da cache é reduzido nunca abaixo o limite de memória do min, conforme definido pela vCores mínimo que pode ser configurado.

Sem servidor e aprovisionado de computação bases de dados, cache entradas podem ser expulsas se toda a memória disponível é utilizada.

#### <a name="cache-hydration"></a>Hidratação de cache

A cache SQL cresce à medida que dados são obtidos a partir do disco da mesma forma e com a mesma velocidade de e para as bases de dados aprovisionadas. Quando a base de dados estiver ocupado, a cache é permitida crescer irrestrita até ao limite máximo de memória.

## <a name="autopausing-and-autoresuming"></a>Autopausing e autoresuming

### <a name="autopausing"></a>Autopausing

Autopausing é acionada se todas as condições seguintes forem verdadeiras durante o atraso de autopause:

- Número sessões = 0
- CPU = 0 para carga de trabalho de utilizador em execução no conjunto de utilizador

É fornecida uma opção de desativar autopausing se assim o desejar.

As seguintes funcionalidades não suportam autopausing.  Ou seja, se qualquer uma das seguintes funcionalidades são usados, em seguida, a base de dados permanece online, independentemente do período de inatividade de base de dados:

- Georreplicação (grupos de Active Directory de georreplicação e ativação pós-falha automática).
- Retenção de cópia de segurança longo prazo (LTR).
- A base de dados de sincronização, utilizado na sincronização de dados SQL.

Autopausing é impedido temporariamente durante a implementação de algumas atualizações de serviço que requerem que a base de dados estar online.  Nesses casos, autopausing torna-se permitido novamente depois de concluída a atualização de serviço.

### <a name="autoresuming"></a>Autoresuming

Autoresuming é acionada se uma das seguintes condições for verdadeira, a qualquer momento:

|Funcionalidade|Acionador de Autoresume|
|---|---|
|Autenticação e autorização|Iniciar sessão|
|Deteção de ameaças|Ativar/desativar o definições de deteção de ameaças ao nível da base de dados ou servidor.<br>Modificar as definições de deteção de ameaças ao nível da base de dados ou servidor.|
|Deteção e classificação de dados|Adicionar, modificar, eliminar ou ao visualizar as etiquetas de sensibilidade|
|Auditoria|Registos de auditorias de visualização.<br>A atualizar ou ao visualizar a diretiva de auditoria.|
|Máscara de dados|Adicionar, modificar, eliminar ou ao visualizar as regras de máscara de dados|
|Encriptação de Dados Transparente|Estado de exibição ou estado de encriptação de dados transparente|
|Arquivo de dados de consultas (desempenho)|Modificar ou ver definições do arquivo de consulta; a otimização automática|
|Autotuning|Aplicação e a verificação de recomendações de autotuning como indexa automaticamente|
|Copiar a base de dados|Crie base de dados como cópia.<br>Exportar para um ficheiro BACPAC.|
|Sincronização de dados SQL|Sincronização entre o hub e member bases de dados que são executados numa agenda configurável ou são executadas manualmente|
|Modificar determinados metadados de base de dados|A adicionar novas marcas de base de dados.<br>Alterar os max vCores, min vCores ou autopause atraso.|
|SQL Server Management Studio (SSMS)|Com o SSMS versão 18 e abrir uma nova janela de consulta para qualquer base de dados no servidor retomará a qualquer base de dados em pausa automaticamente no mesmo servidor. Este comportamento não ocorre se com o SSMS versão 17.9.1 com o IntelliSense ativado-off.|

Autoresuming também é acionado durante a implementação de algumas atualizações de serviço que requerem que a base de dados estar online.

### <a name="connectivity"></a>Conectividade

Se uma base de dados sem servidor está em pausa, o primeiro início de sessão retomar a base de dados e devolver um erro a indicar que a base de dados não está disponível com o código de erro 40613. Assim que a base de dados é retomada, o início de sessão deve ser repetido para estabelecer conectividade. Os clientes da base de dados com a lógica de repetição de ligação não deve ser modificado.

### <a name="latency"></a>Latência

A latência autoresume e autopause uma base de dados sem servidor é, geralmente, a ordem de 1 minuto para autoresume e 1 a 10 minutos para autopause.

## <a name="onboarding-into-serverless-compute-tier"></a>Integração na camada de computação sem servidor

Criar uma nova base de dados ou para mover a que base de dados existente para um escalão de computação sem servidor segue o mesmo padrão usados para criar uma nova base de dados aprovisionada a camada de computação e envolve os seguintes dois passos.

1. Especifique o nome do objetivo de serviço. O objetivo de serviço prescreve a camada de serviços, a geração de hardware e o vCores máximo. A tabela seguinte mostra as opções de objetivo de serviço:

   |Nome do objetivo de serviço|Camada de serviços|Geração de hardware|Máx. vCores|
   |---|---|---|---|
   |GP_S_Gen5_1|Fins Gerais|Gen5|1|
   |GP_S_Gen5_2|Fins Gerais|Gen5|2|
   |GP_S_Gen5_4|Fins Gerais|Gen5|4|

2. Opcionalmente, especifica o atraso de vCores e autopause min para alterar os valores predefinidos. A tabela seguinte mostra os valores disponíveis para esses parâmetros.

   |Parâmetro|Opções de valor|Valor predefinido|
   |---|---|---|---|
   |Min vCores|Qualquer um dos {0,5, 1, 2, 4} que não exceda vCores máx.|0,5 vCores|
   |Autopause atraso|Mínimo: 60 minutos (1 hora)<br>Máximo: 10080 minutos (7 dias)<br>Incrementos: 60 minutos<br>Desativar autopause: -1|60 minutos|

> [!NOTE]
> Com o T-SQL para mover uma base de dados existente para sem servidor ou alterar seu tamanho de computação não é atualmente suportada, mas pode ser feito através do portal do Azure ou o PowerShell.

### <a name="create-new-database-in-serverless-compute-tier"></a>Criar nova base de dados na camada de computação sem servidor 

#### <a name="use-azure-portal"></a>Utilizar o portal do Azure

Consulte [início rápido: Criar uma base de dados na base de dados do SQL do Azure no portal do Azure](sql-database-single-database-get-started.md).

#### <a name="use-powershell"></a>Utilizar o PowerShell

O exemplo seguinte cria uma nova base de dados na camada de computação sem servidor.  Este exemplo especifica explicitamente o min vCores, max vCores e autopause atraso.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Mover a base de dados da camada de computação aprovisionada no escalão de computação sem servidor

#### <a name="use-powershell"></a>Utilizar o PowerShell

O exemplo seguinte move uma base de dados de camada de computação aprovisionada para a camada de computação sem servidor. Este exemplo especifica explicitamente o min vCores, max vCores e autopause atraso.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Mover a base de dados da camada de computação sem servidor na camada de computação aprovisionada

Uma base de dados sem servidor pode ser movido para um escalão de computação aprovisionada da mesma forma como mover uma base de dados de computação aprovisionada numa camada de computação sem servidor.

## <a name="modifying-serverless-configuration"></a>Modificar configuração sem servidor

### <a name="maximum-vcores"></a>Máximo de vCores

#### <a name="use-powershell"></a>Utilizar o PowerShell

Modificar os vCores máximos é executada com o [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando no PowerShell com o `MaxVcore` argumento.

### <a name="minimum-vcores"></a>Mínimo de vCores

#### <a name="use-powershell"></a>Utilizar o PowerShell

Modificar os vCores min é executada com o [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando no PowerShell com o `MinVcore` argumento.

### <a name="autopause-delay"></a>Autopause atraso

#### <a name="use-powershell"></a>Utilizar o PowerShell

Modificar o atraso de autopause é executada com o [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando no PowerShell com o `AutoPauseDelayInMinutes` argumento.

## <a name="monitoring"></a>Monitorização

### <a name="resources-used-and-billed"></a>Recursos utilizados e faturadas

Os recursos de uma base de dados sem servidor são encapsulados por pacote de aplicação, a instância SQL e entidades de agrupamento de recursos de utilizador.

#### <a name="app-package"></a>Pacote de aplicação

O pacote de aplicação é o limite da gestão recursos, a maioria dos e externa, uma base de dados, independentemente se a base de dados está numa camada de computação sem servidor ou aprovisionado. O pacote de aplicação contém a instância SQL e desse escopo em conjunto de serviços externo todos os recursos de utilizador e do sistema, utilizados por uma base de dados na base de dados do SQL. Exemplos de serviços externos incluem R e a pesquisa em texto completo. A instância SQL geralmente domina a utilização de recursos geral entre o pacote de aplicação.

#### <a name="user-resource-pool"></a>Agrupamento de recursos de utilizador

O agrupamento de recursos de utilizador é interno, a maioria dos limites de gestão de recursos para uma base de dados, independentemente se a base de dados está numa camada de computação sem servidor ou aprovisionado. Os utilizador resource pool âmbitos de CPU e e/s para carga de trabalho de utilizador gerada por consultas DDL, como consultas de criar e ALTER e DML, tais como SELECIONAR, inserir, ATUALIZAR e eliminar. Estas consultas geralmente representam a proporção mais substancial de utilização dentro do pacote de aplicação.

### <a name="metrics"></a>Métricas

As métricas para monitorizar a utilização de recursos do agrupamento de pacote e um utilizador de aplicação de uma base de dados sem servidor estão listadas na tabela a seguir:

|Entidade|Métrica|Descrição|Unidades|
|---|---|---|---|
|Pacote de aplicação|app_cpu_percent|Percentagem de vCores utilizados pela aplicação em relação ao vCores máximo permitido para a aplicação.|Percentagem|
|Pacote de aplicação|app_cpu_billed|A quantidade de computação cobrada para a aplicação durante o período de relatório. O valor pago durante este período é o produto desta métrica e o preço unitário de vCore. <br><br>Valores desta métrica são determinados através da agregação ao longo do tempo utilizado o número máximo de CPU e memória utilizada por segundo. Se a quantidade utilizada é inferior à quantidade mínima aprovisionada conforme definido pela min vCores e memória mínima, a quantidade mínima aprovisionada é faturada. Para comparar CPU com memória para efeitos de faturação, memória é normalizada em unidades de vCores ao redimensionar a quantidade de memória em GB por 3 GB por vCore.|segundos de vCore|
|Pacote de aplicação|app_memory_percent|Percentagem de memória utilizada pela aplicação em relação ao máxima de memória permitido para a aplicação.|Percentagem|
|Conjunto de utilizador|cpu_percent|Percentagem de vCores utilizados por carga de trabalho do utilizador em relação ao vCores máximo permitido para a carga de trabalho do utilizador.|Percentagem|
|Conjunto de utilizador|data_IO_percent|Percentagem dos dados utilizados por carga de trabalho do utilizador em relação ao dados máximo IOPS de IOPS permitido para a carga de trabalho do utilizador.|Percentagem|
|Conjunto de utilizador|log_IO_percent|Percentagem de log MB/s utilizado por carga de trabalho do utilizador em relação ao MB/s de registo máximo permitido para carga de trabalho do utilizador.|Percentagem|
|Conjunto de utilizador|workers_percent|Percentagem de trabalhadores utilizados por carga de trabalho do utilizador em relação ao trabalhadores máximos permitido para a carga de trabalho do utilizador.|Percentagem|
|Conjunto de utilizador|sessions_percent|Percentagem de sessões utilizado por carga de trabalho do utilizador em relação ao sessões máximo permitido para a carga de trabalho do utilizador.|Percentagem|

### <a name="pause-and-resume-status"></a>Colocar em pausa e retomar do Estado

No portal do Azure, o estado da base de dados é apresentado no painel de descrição geral do servidor que apresenta uma lista de bases de dados que nele contidos. O estado de banco de dados também é apresentado no painel de descrição geral da base de dados.

Com o seguinte comando do PowerShell para consultar a pausa e retomar do Estado de uma base de dados:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Limites de recursos

Para limites de recursos, consulte [escalão de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>Faturação

A quantidade de computação faturada é o número máximo de utilização de CPU e memória utilizada por segundo. Se utilizado a quantidade de CPU e memória utilizada é inferior à quantidade mínima aprovisionada para cada um, a quantidade aprovisionada é faturada. Para comparar CPU com memória para efeitos de faturação, memória é normalizada em unidades de vCores ao redimensionar a quantidade de memória em GB por 3 GB por vCore.

- **Recurso faturado**: CPU e memória
- **Montante faturado**: preço unitário de vCore * máximo (min vCores, vCores utilizados, a memória mínima GB * 1/3, memória GB utilizados * 1/3) 
- **Frequência de faturação**: Por segundo

O preço unitário de vCore no custo por vCore por segundo. Consulte a [página de preços do Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) para obter preços de unidade específica numa determinada região.

A quantidade de computação faturada é exposta pela métrica seguinte:

- **Métrica**: app_cpu_billed (vCore segundos)
- **Definição**: máximo (min vCores, vCores utilizados, a memória mínima GB * 1/3, memória GB utilizados * 1/3)
- **Frequência de relatório**: Por minuto

Esta quantidade é calculada a cada segundo e agregada de mais de 1 minuto.

Considere uma base de dados sem servidor configurado com 1 min vCore e 4 vCores máximos.  Isso corresponde a cerca de 3 GB de memória mínima e máximas de 12 GB de memória.  Suponha que o atraso de pausa automática está definido para 6 horas e a carga de trabalho de base de dados esteve ativa durante as primeiras 2 horas de um período de 24 horas e caso contrário, inativos.    

Neste caso, a base de dados é cobrada por computação e armazenamento, durante as primeiras 8 horas.  Apesar da base de dados é iniciar inativo após na segunda hora, ainda é faturada para computação nas horas 6 subsequentes com base na computação mínimo aprovisionada enquanto a base de dados está online.  Apenas o armazenamento é cobrado durante o restante do período de 24 horas enquanto a base de dados está em pausa.

Mais precisamente, a fatura de computação neste exemplo é calculada da seguinte forma:

|Intervalo de tempo|vCores utilizados por segundo|GB utilizados por segundo|Dimensão faturada de computação|segundos de vCore faturados ao longo do intervalo de tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores utilizados|4 vCores * 3600 segundos = 14400 vCore segundos|
|1:00-2:00|1|12|Memória utilizada|12 GB * 1/3 * 3600 segundos = 14400 vCore segundos|
|2:00-8:00|0|0|Memória mínima aprovisionada|3 GB * 1/3 * 21600 segundos = 21600 vCore segundos|
|8:00-24:00|0|0|Nenhuma computação faturada ao passo que está em pausa|vCore de 0 segundos|
|Segundos de total vCore faturados mais de 24 horas||||50400 vCore segundos|

Suponha que o preço de unidade de computação é $0.000073/vCore/second.  Em seguida, a computação faturada durante este período de 24 horas é o produto das computação unidade preço e vCore segundos faturado: $0.000073/vCore/second * 50400 segundos de vCore = us $3,68

## <a name="available-regions"></a>Regiões disponíveis

O escalão de computação sem servidor está disponível em todo o mundo, exceto as seguintes regiões: Leste da Austrália Central, China, Norte da China, Sul de França, Alemanha Central, Alemanha Nordeste, Índia Ocidental, Sul da Coreia, África do Sul Ocidental, norte do Reino Unido, sul do Reino Unido, oeste do Reino Unido e EUA Centro-Oeste.

## <a name="next-steps"></a>Passos seguintes

- Para começar a utilizar, consulte o artigo [início rápido: Criar uma base de dados na base de dados do SQL do Azure no portal do Azure](sql-database-single-database-get-started.md).
- Para limites de recursos, consulte [limites de recursos de camada de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).

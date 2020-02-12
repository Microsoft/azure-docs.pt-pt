---
title: Sem servidor
description: Este artigo descreve o novo nível de computação sem servidor e compara-o com o nível de computação aprovisionado existente
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: 750d08f3667317e9e1e396cff50884101d7ff55d
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131957"
---
# <a name="azure-sql-database-serverless"></a>Base de dados Azure SQL sem servidor

O servidor da Base de Dados Azure SQL é um nível de cálculo para bases de dados únicas que escala automaticamente a computação com base na procura de carga de trabalho e nas faturas para a quantidade de cálculo utilizada por segundo. O nível de computação sem servidor também interrompe automaticamente as bases de dados durante períodos inativos quando apenas o armazenamento é faturado e retoma automaticamente as bases de dados quando a atividade retorna.

## <a name="serverless-compute-tier"></a>Escalão de serviço de computação sem servidor

O nível de computação sem servidor para uma única base de dados é parametrizado por uma gama de autoscalcificação computacional e um atraso de auto-pausa.  A configuração destes parâmetros molda a experiência de desempenho da base de dados e o custo do cálculo.

![faturação sem servidor](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Configuração de desempenho

- Os **vCores mínimos** e **os vCores máximos** são parâmetros configuráveis que definem a gama de capacidade computacional disponível para a base de dados. Os limites de memória e IO são proporcionais à gama vCore especificada.  
- O atraso de **pausa automática** é um parâmetro configurável que define o período de tempo em que a base de dados deve estar inativa antes de ser automaticamente interrompida. A base de dados é retomada automaticamente quando ocorre o próximo login ou outra atividade.  Em alternativa, a utilização automática pode ser desativada.

### <a name="cost"></a>Custo

- O custo de uma base de dados sem servidores é a soma do custo do cálculo e do custo de armazenamento.
- Quando o uso da computação está entre os limites min e máximos configurados, o custo do cálculo baseia-se no vCore e na memória utilizada.
- Quando o uso do cálculo está abaixo dos limites de min configurados, o custo do cálculo baseia-se nos min vCores e na memória min configurada.
- Quando a base de dados é interrompida, o custo do cálculo é zero e apenas os custos de armazenamento são incorridos.
- O custo de armazenagem é determinado da mesma forma que no nível de cálculo previsto.

Para mais detalhes de custos, consulte [Billing](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Cenários

Serverless é o desempenho de preço otimizado para bases de dados individuais com padrões de utilização intermitentes e imprevisíveis que podem suportar algum atraso no aquecimento da computação após períodos de utilização inativo. Em contrapartida, o nível de cálculo provisionado é o desempenho do preço otimizado para bases de dados individuais ou múltiplas bases de dados em piscinas elásticas com uma utilização média mais elevada que não pode permitir qualquer atraso no aquecimento da computação.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Cenários bem adaptados para computação sem servidor

- Bases de dados únicas com padrões de utilização intermitentes e imprevisíveis intercalados com períodos de inatividade e menor utilização média da computação ao longo do tempo.
- Bases de dados únicas no nível de cálculo provisionado que são frequentemente redimensionados e clientes que preferem delegar a edição do serviço.
- Novas bases de dados únicas sem histórico de utilização onde o dimensionamento de cálculo é difícil ou não é possível estimar antes da implantação na Base de Dados SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Cenários bem adaptados para computação aprovisionada

- Bases de dados individuais com padrões de utilização mais regulares e previsíveis e utilização média mais alta da computação ao longo do tempo.
- Bases de dados que não podem tolerar compensações de desempenho resultantes de aparar ou atrasar a memória mais frequente suportável ou atrasar o ressumamento automático de um estado de pausa.
- Múltiplas bases de dados com padrões de utilização intermitentes e imprevisíveis que podem ser consolidadas em piscinas elásticas para uma melhor otimização de desempenho de preço.

## <a name="comparison-with-provisioned-compute-tier"></a>Comparação com o nível de cálculo provisionado

O quadro seguinte resume distinções entre o nível de computação sem servidor e o nível de cálculo provisionado:

| | **Computação sem servidor** | **Cálculo provisionado** |
|:---|:---|:---|
|**Padrão de utilização da base de dados**| Uso intermitente e imprevisível com menor utilização média de cálculo ao longo do tempo. |  Padrões de utilização mais regulares com uma utilização média mais alta da computação ao longo do tempo, ou várias bases de dados usando piscinas elásticas.|
| **Esforço de gestão de desempenho** |Mais baixo|Mais alto|
|**Escala de computação**|Automático|Manual|
|**Capacidade de resposta computacional**|Menor após períodos inativos|Imediata|
|**Granularidade de faturação**|Por segundo|Por hora|

## <a name="purchasing-model-and-service-tier"></a>Modelo de compra e nível de serviço

O servidor da Base de Dados SQL é atualmente suportado apenas no nível De Propósito Geral no hardware da Geração 5 no modelo de compra vCore.

## <a name="autoscaling"></a>Dimensionamento automático

### <a name="scaling-responsiveness"></a>Capacidade de resposta de escala

Em geral, as bases de dados sem servidores são executadas numa máquina com capacidade suficiente para satisfazer a procura de recursos sem interrupção de qualquer quantidade de computação solicitada dentro dos limites estabelecidos pelo valor vCores max. Ocasionalmente, o equilíbrio de carga ocorre automaticamente se a máquina não conseguir satisfazer a procura de recursos dentro de alguns minutos. Por exemplo, se a procura de recursos for de 4 vCores, mas apenas 2 vCores estiverem disponíveis, então pode levar até alguns minutos para carregar o equilíbrio antes de serem fornecidos 4 vCores. A base de dados permanece on-line durante o equilíbrio de carga, exceto por um breve período no final da operação quando as ligações são retiradas.

### <a name="memory-management"></a>Gestão da memória

A memória das bases de dados sem servidores é recuperada com mais frequência do que para bases de dados de cálculo provisionadas. Este comportamento é importante para controlar os custos no servidor e pode impactar o desempenho.

#### <a name="cache-reclamation"></a>Recuperação de cache

Ao contrário das bases de dados computacionais aprovisionadas, a memória da cache SQL é recuperada a partir de uma base de dados sem servidores quando a utilização de CPU ou cache é baixa.

- A utilização do cache é considerada baixa quando o tamanho total das entradas de cache mais recentemente utilizadas fica abaixo de um limiar por um período de tempo.
- Quando a recuperação da cache é desencadeada, o tamanho da cache-alvo é reduzido gradualmente a uma fração do seu tamanho anterior e a recuperação só continua se o uso permanecer baixo.
- Quando ocorre a recuperação de cache, a política de seleção de entradas de cache para despejo é a mesma política de seleção que para bases de dados de cálculo aprovisionadas quando a pressão da memória é alta.
- O tamanho da cache nunca é reduzido abaixo do limite de memória min, tal como definido por min vCores que podem ser configurados.

Nas bases de dados computacionais sem servidor estoma e aprovisionados, as entradas em cache podem ser despejadas se toda a memória disponível for utilizada.

#### <a name="cache-hydration"></a>Hidratação em cache

A cache SQL cresce à medida que os dados são recolhidos do disco da mesma forma e com a mesma velocidade que para as bases de dados aprovisionadas. Quando a base de dados está ocupada, a cache pode crescer sem restrições até ao limite máximo de memória.

## <a name="autopausing-and-autoresuming"></a>Autopaus e auto-retoma

### <a name="autopausing"></a>Autopausing

A utilização automática é desencadeada se todas as seguintes condições forem verdadeiras durante a duração do atraso de pausa automática:

- Sessões de número = 0
- CPU = 0 para a carga de trabalho do utilizador que funciona na piscina do utilizador

É fornecida uma opção para desativar a auto-pausa, se desejar.

As seguintes funcionalidades não suportam a utilização automática.  Ou seja, se alguma das seguintes funcionalidades for utilizada, a base de dados permanece online independentemente da duração da inatividade da base de dados:

- Geo-replicação (geo-replicação ativa e grupos de auto-failover).
- Retenção de backup a longo prazo (LTR).
- A base de dados de sincronização utilizada na sincronização de dados SQL.  Ao contrário das bases de dados de sincronização, as bases de dados do hub e dos membros suportam a utilização automática.
- A base de dados de emprego usada em trabalhos elásticos.

A utilização automática é temporariamente evitada durante a implementação de algumas atualizações de serviço que requerem que a base de dados esteja online.  Nesses casos, a utilização automática torna-se novamente permitida assim que a atualização do serviço estiver concluída.

### <a name="autoresuming"></a>Auto-presumindo

O pressuposto automático é desencadeado se alguma das seguintes condições for verdadeira a qualquer momento:

|Funcionalidade|Gatilho de reinício automático|
|---|---|
|Autenticação e autorização|Iniciar sessão|
|Deteção de ameaças|Ativar/desativar as definições de deteção de ameaças na base de dados ou no nível do servidor.<br>Modificando as definições de deteção de ameaças na base de dados ou no nível do servidor.|
|Deteção e classificação de dados|Adicionar, modificar, apagar ou visualizar etiquetas de sensibilidade|
|Auditoria|A ver os registos de auditoria.<br>Atualizar ou visualizar a política de auditoria.|
|Máscara de dados|Adicionar, modificar, apagar ou ver regras de mascaramento de dados|
|Encriptação de Dados Transparente|Ver estado ou estado de encriptação de dados transparentes|
|Consulta (performance) loja de dados|Modificação ou visualização das definições de loja de consulta|
|Auto-sintonia|Aplicação e verificação de recomendações de afinação automática, tais como a indexação automática|
|Cópia da base de dados|Criar base de dados como cópia.<br>Exportar para um ficheiro BACPAC.|
|Sincronização de dados SQL|Sincronização entre as bases de dados do hub e dos membros que funcionam numa programação configurável ou são realizadas manualmente|
|Modificação de certos metadados de base de dados|Adicionando novas etiquetas de base de dados.<br>Alterar vCores max, min vCores ou atraso de pausa automática.|
|SQL Server Management Studio (SSMS)|A utilização de versões SSMS antes do dia 18.1 e a abertura de uma nova janela de consulta para qualquer base de dados no servidor retomará qualquer base de dados de pausa automática no mesmo servidor. Este comportamento não ocorre se utilizar a versão SSMS 18.1 ou posterior.|

O auto-aviso também é desencadeado durante a implementação de algumas atualizações de serviço que exigem que a base de dados esteja online.

### <a name="connectivity"></a>Conectividade

Se uma base de dados sem servidor for interrompida, o primeiro login retomará a base de dados e devolverá um erro indicando que a base de dados não está disponível com o código de erro 40613. Uma vez retomada a base de dados, o login deve ser novamente tentado para estabelecer a conectividade. Os clientes da base de dados com lógica de retry de ligação não devem ser modificados.

### <a name="latency"></a>Latência

A latência para reretomar automaticamente e fazer uma pausa automática numa base de dados sem servidores é geralmente ordem de 1 minuto para reretomar automaticamente e 1-10 minutos para fazer uma pausa automática.

## <a name="onboarding-into-serverless-compute-tier"></a>Embarque em nível de computação sem servidor

A criação de uma nova base de dados ou a mudança de uma base de dados existente para um nível de computação sem servidor segue o mesmo padrão que a criação de uma nova base de dados no nível de cálculo aprovisionado e envolve os dois passos seguintes.

1. Especifique o nome objetivo do serviço. O objetivo de serviço prescreve o nível de serviço, geração de hardware e vCores max. A tabela que se segue mostra as opções objetivas do serviço:

   |Nome objetivo de serviço|Camada de serviços|Geração de hardware|Max vCores|
   |---|---|---|---|
   |GP_S_Gen5_1|Fins Gerais|Gen5|1|
   |GP_S_Gen5_2|Fins Gerais|Gen5|2|
   |GP_S_Gen5_4|Fins Gerais|Gen5|4|
   |GP_S_Gen5_6|Fins Gerais|Gen5|6|
   |GP_S_Gen5_8|Fins Gerais|Gen5|8|
   |GP_S_Gen5_10|Fins Gerais|Gen5|10|
   |GP_S_Gen5_12|Fins Gerais|Gen5|12|
   |GP_S_Gen5_14|Fins Gerais|Gen5|14|
   |GP_S_Gen5_16|Fins Gerais|Gen5|16|

2. Opcionalmente, especifique o min vCores e o atraso de pausa automática para alterar os seus valores predefinidos. A tabela seguinte mostra os valores disponíveis para estes parâmetros.

   |Parâmetro|Escolhas de valor|Valor predefinido|
   |---|---|---|---|
   |Min vCores|Depende de max vCores configurados - ver [limites de recursos](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).|0,5 vCores|
   |Atraso de pausa automática|Mínimo: 60 minutos (1 hora)<br>Máximo: 10080 minutos (7 dias)<br>Incrementos: 60 minutos<br>Desativar a pausa automática: -1|60 minutos|


### <a name="create-new-database-in-serverless-compute-tier"></a>Criar nova base de dados no nível de computação sem servidor 

Os exemplos seguintes criam uma nova base de dados no nível de computação sem servidor. Os exemplos especificam explicitamente os min vCores, max vCores e atraso de pausa automática.

#### <a name="use-azure-portal"></a>Utilizar o portal do Azure

Ver [Quickstart: Criar uma única base de dados na Base de Dados Azure SQL utilizando o portal Azure](sql-database-single-database-get-started.md).


#### <a name="use-powershell"></a>Utilizar o PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>Utilizar a CLI do Azure

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Utilizar transact-SQL (T-SQL)

O exemplo seguinte cria uma nova base de dados no nível de computação sem servidor.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Para mais detalhes, consulte [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Mover base de dados do nível de cálculo provisionado para o nível de computação sem servidor

Os exemplos seguintes movem uma base de dados do nível de cálculo provisionado para o nível de computação sem servidor. Os exemplos especificam explicitamente os min vCores, max vCores e atraso de pausa automática.

#### <a name="use-powershell"></a>Utilizar o PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>Utilizar a CLI do Azure

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Utilizar transact-SQL (T-SQL)

O exemplo seguinte move uma base de dados do nível de cálculo provisionado para o nível de computação sem servidor.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Para mais detalhes, consulte [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Mover base de dados do nível de computação sem servidor para o nível de cálculo provisionado

Uma base de dados sem servidores pode ser transferida para um nível de computação provisionado da mesma forma que mover uma base de dados de cálculo provisionado para um nível de computação sem servidor.

## <a name="modifying-serverless-configuration"></a>Modificação da configuração sem servidor

### <a name="use-powershell"></a>Utilizar o PowerShell

A modificação dos vCores máximos ou mínimos e atraso de pausa automática é realizada utilizando o comando [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) no PowerShell utilizando os argumentos `MaxVcore`, `MinVcore`e `AutoPauseDelayInMinutes`.

### <a name="use-azure-cli"></a>Utilizar a CLI do Azure

A modificação dos vCores máximos ou mínimos, e atraso de pausa automática, é realizada utilizando o comando de [atualização az sql db](/cli/azure/sql/db#az-sql-db-update) em Azure CLI utilizando os argumentos `capacity`, `min-capacity`e `auto-pause-delay`.


## <a name="monitoring"></a>Monitorização

### <a name="resources-used-and-billed"></a>Recursos utilizados e faturados

Os recursos de uma base de dados sem servidores são encapsulados por pacote de aplicativos, instância SQL e entidades de piscina de recursos de utilizador.

#### <a name="app-package"></a>Pacote de aplicativos

O pacote de aplicações é o limite de gestão de recursos mais exterior para uma base de dados, independentemente de a base de dados estar num nível de computação sem servidorou aprovisionado. O pacote de aplicações contém a instância SQL e os serviços externos que, em conjunto, âmbito de todos os recursos de utilizador e sistema utilizados por uma base de dados na Base de Dados SQL. Exemplos de serviços externos incluem pesquisa de Texto R e texto completo. A instância SQL geralmente domina a utilização global de recursos em todo o pacote de aplicações.

#### <a name="user-resource-pool"></a>Piscina de recursos do utilizador

O conjunto de recursos do utilizador é o limite de gestão mais interior de recursos para uma base de dados, independentemente de a base de dados estar num nível de computação sem servidorou aprovisionado. O conjunto de recursos do utilizador scopes CPU e IO para a carga de trabalho do utilizador gerado por consultas DDL tais como consultas CREATE e ALTER e DML tais como SELECT, INSERT, UPDATE e DELETE. Estas consultas representam geralmente a proporção mais substancial de utilização dentro do pacote de aplicações.

### <a name="metrics"></a>Métricas

As métricas para monitorizar a utilização de recursos do pacote de aplicações e o conjunto de utilizadores de uma base de dados sem servidorestão listadas na tabela seguinte:

|Entidade|Métrica|Descrição|Unidades|
|---|---|---|---|
|Pacote de aplicativos|app_cpu_percent|Percentagem de vCores utilizados pela app em relação a vCores max permitidos para a aplicação.|Percentagem|
|Pacote de aplicativos|app_cpu_billed|A quantidade de cálculo faturado para a app durante o período de reporte. O valor pago durante este período é o produto desta métrica e o preço unitário vCore. <br><br>Os valores desta métrica são determinados por agregação ao longo do tempo o máximo de CPU utilizado e memória usada a cada segundo. Se o montante utilizado for inferior ao montante mínimo previsto pelos min vCores e pela memória min, então o montante mínimo previsto é faturado. Para comparar cpU com memória para efeitos de faturação, a memória é normalizada em unidades de vCores, rescalando a quantidade de memória em GB por 3 GB por vCore.|vCore segundos|
|Pacote de aplicativos|app_memory_percent|Percentagem de memória utilizada pela app em relação à memória máxima permitida para a aplicação.|Percentagem|
|Piscina de utilizadores|cpu_percent|Percentagem de vCores utilizados pela carga de trabalho do utilizador em relação ao vCores max permitido para a carga de trabalho do utilizador.|Percentagem|
|Piscina de utilizadores|data_IO_percent|Percentagem de dados iOPS utilizados pela carga de trabalho do utilizador em relação aos iOPS de dados máximos permitidos para a carga de trabalho do utilizador.|Percentagem|
|Piscina de utilizadores|log_IO_percent|Percentagem de MB/s de registo utilizado pela carga de trabalho do utilizador em relação ao registo máximo MB/s permitido para a carga de trabalho do utilizador.|Percentagem|
|Piscina de utilizadores|workers_percent|Percentagem de trabalhadores utilizados por carga de trabalho do utilizador relativamente aos trabalhadores max permitidos para a carga de trabalho dos utilizadores.|Percentagem|
|Piscina de utilizadores|sessions_percent|Percentagem de sessões utilizadas pela carga horária do utilizador relativamente a sessões máximas permitidas para a carga de trabalho do utilizador.|Percentagem|

### <a name="pause-and-resume-status"></a>Pausa e retoma do estado

No portal Azure, o estado da base de dados é apresentado no painel de visão geral do servidor que lista as bases de dados que contém. O estado da base de dados também é apresentado no painel de visão geral para a base de dados.

Utilizando os seguintes comandos para consultar a pausa e retomar o estado de uma base de dados:

#### <a name="use-powershell"></a>Utilizar o PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-azure-cli"></a>Utilizar a CLI do Azure

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Limites de recursos

Para obter limites de recursos, consulte o nível de [computação sem servidores](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Faturação

A quantidade de cálculo faturada é o máximo de CPU utilizado e memória usada a cada segundo. Se o montante da CPU utilizada e da memória utilizada for inferior ao montante mínimo previsto para cada um, o montante previsto é faturado. Para comparar cpU com memória para efeitos de faturação, a memória é normalizada em unidades de vCores, rescalando a quantidade de memória em GB por 3 GB por vCore.

- **Faturação de recursos**: CPU e memória
- **Valor faturado:** vCore preço unitário * máx (min vCores, vCores utilizados, memória min GB * 1/3, memória GB utilizada * 1/3) 
- **Frequência de faturação**: Por segundo

O preço unitário vCore é o custo por vCore por segundo. Consulte a página de preços da Base de [Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/) para preços unitários específicos numa determinada região.

A quantidade de cálculo faturada é exposta pela seguinte métrica:

- **Métrica**: app_cpu_billed (vCore segundos)
- **Definição:** máx (min vCores, vCores utilizados, memória min GB * 1/3, memória GB utilizada * 1/3)
- **Frequência de reporte**: Por minuto

Esta quantidade é calculada a cada segundo e agregada ao longo de 1 minuto.

Considere uma base de dados sem servidorconfigurada com 1 min vCore e 4 vCores max.  Isto corresponde a cerca de 3 GB de memória min e memória máxima de 12 GB.  Suponha que o atraso de pausa automática esteja definido para 6 horas e que a carga de trabalho da base de dados esteja ativa durante as primeiras 2 horas de um período de 24 horas e de outra forma inativa.    

Neste caso, a base de dados é cobrada para computação e armazenamento durante as primeiras 8 horas.  Embora a base de dados esteja inativa a partir da segunda hora, ainda é faturada para computação nas 6 horas subsequentes com base no cálculo mínimo provisionado enquanto a base de dados está online.  Apenas o armazenamento é faturado durante o restante período de 24 horas enquanto a base de dados é interrompida.

Mais precisamente, a conta do cálculo neste exemplo é calculada da seguinte forma:

|Intervalo de Tempo|vCores usados a cada segundo|GB usado a cada segundo|Dimensão computacional faturada|vCore segundos faturados ao longo do intervalo de tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores utilizados|4 vCores * 3600 segundos = 14400 vCore segundos|
|1:00-2:00|1|12|Memória usada|12 GB * 1/3 * 3600 segundos = 14400 vCore segundos|
|2:00-8:00|0|0|Memória min aprovisionada|3 GB * 1/3 * 21600 segundos = 21600 vCore segundos|
|8:00-24:00|0|0|Sem cálculo faturado enquanto pausado|0 vCore segundos|
|Total de segundos vCore faturados ao longo de 24 horas||||50400 vCore segundos|

Suponha que o preço unitário do cálculo seja $0.000145/vCore/segundo.  Em seguida, o cálculo faturado para este período de 24 horas é o produto do preço unitário do computação e vCore segundos cobrados: $0.000145/vCore/segundo * 50400 vCore segundos ~ $7,31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Benefício Híbrido Azure e capacidade reservada

O Azure Hybrid Benefit (AHB) e os descontos de capacidade reservados não se aplicam ao nível de computação sem servidor.

## <a name="available-regions"></a>Regiões disponíveis

O nível de computação sem servidores está disponível em todo o mundo, exceto as seguintes regiões: China East, China North, Alemanha Central, Alemanha Nordeste, Reino Unido Norte, Reino Unido Sul 2, Oeste Central DOS EUA e EUA Gov Central (Iowa).

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [Quickstart: Crie uma única base de dados na Base de Dados Azure SQL utilizando o portal Azure](sql-database-single-database-get-started.md).
- Para limites de recursos, consulte os limites de recursos de [nível de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).

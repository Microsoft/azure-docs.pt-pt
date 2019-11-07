---
title: Base de Dados SQL do Azure sem servidor
description: Este artigo descreve a nova camada de computação sem servidor e a compara com a camada de computação provisionada existente
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: moslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 11/04/2019
ms.openlocfilehash: fcd79182e046d94f9e67acecebd5cf6a45f2706f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687396"
---
# <a name="azure-sql-database-serverless"></a>Base de Dados SQL do Azure sem servidor

O banco de dados SQL do Azure sem servidor é uma camada de computação para bancos únicos de dados que dimensionam automaticamente a computação com base na demanda de carga de trabalho e cobra pela quantidade de computação usada por segundo. A camada de computação sem servidor também pausa automaticamente os bancos de dados durante períodos inativos quando apenas o armazenamento é cobrado e retoma automaticamente os bancos de dados quando a atividade retorna.

## <a name="serverless-compute-tier"></a>Escalão de serviço de computação sem servidor

A camada de computação sem servidor para um banco de dados individual é parametrizada por um intervalo de dimensionamento automático de computação e um atraso de pausa automático.  A configuração desses parâmetros forma a experiência de desempenho do banco de dados e o custo de computação.

![cobrança sem servidor](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Configuração de desempenho

- O **vCores mínimo** e o **máximo de vCores** são parâmetros configuráveis que definem o intervalo de capacidade de computação disponível para o banco de dados. Os limites de memória e e/s são proporcionais ao intervalo vCore especificado.  
- O **atraso de pausa** automática é um parâmetro configurável que define o período de tempo que o banco de dados deve ficar inativo antes de ser pausado automaticamente. O banco de dados é retomado automaticamente quando ocorre o próximo logon ou outra atividade.  Como alternativa, a autopausa pode ser desabilitada.

### <a name="cost"></a>Custo

- O custo de um banco de dados sem servidor é o somatório do custo de computação e do custo de armazenamento.
- Quando o uso de computação está entre os limites mínimo e máximo configurados, o custo de computação é baseado em vCore e na memória usada.
- Quando o uso de computação estiver abaixo dos limites mínimos configurados, o custo de computação será baseado no mínimo de vCores e mín. de memória configurada.
- Quando o banco de dados é pausado, o custo de computação é zero e apenas os custos de armazenamento são incorridos.
- O custo de armazenamento é determinado da mesma forma que na camada de computação provisionada.

Para obter mais detalhes, consulte [cobrança](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Cenários

O desempenho sem servidor é otimizado para um único banco de dados com padrões de uso intermitentes e imprevisíveis que podem arcar com algum atraso no cálculo de pico após períodos de uso ociosos. Por outro lado, a camada de computação provisionada é otimizada para o preço/desempenho para bancos de dados individuais ou para vários bancos de dados em pools elásticos com maior uso médio que não pode arcar com nenhum atraso no aquecimento de computação.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Cenários bem adequados para computação sem servidor

- Bancos de dados individuais com padrões de uso intermitentes e imprevisíveis intercalados com períodos de inatividade e menor utilização média de computação ao longo do tempo.
- Bancos de dados individuais na camada de computação provisionada que são frequentemente redimensionados e clientes que preferem delegar a redimensionamento de computação para o serviço.
- Novos bancos de dados individuais sem histórico de uso, em que o dimensionamento de computação é difícil ou não é possível estimar antes da implantação no banco de dados SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Cenários bem adequados para computação provisionada

- Bancos de dados individuais com padrões de uso mais regulares e previsíveis e maior utilização média de computação ao longo do tempo.
- Bancos de dados que não podem tolerar compensações de desempenho resultantes de corte de memória mais frequente ou atraso na retomada de um estado em pausa.
- Vários bancos de dados com padrões de uso intermitentes e imprevisíveis que podem ser consolidados em pools elásticos para melhorar a otimização do preço-desempenho.

## <a name="comparison-with-provisioned-compute-tier"></a>Comparação com a camada de computação provisionada

A tabela a seguir resume as distinções entre a camada de computação sem servidor e a camada de computação provisionada:

| | **Computação sem servidor** | **Computação provisionada** |
|:---|:---|:---|
|**Padrão de uso do banco de dados**| Uso intermitente e imprevisível com menor utilização média de computação ao longo do tempo. |  Padrões de uso mais regulares com maior utilização média de computação ao longo do tempo ou a vários bancos de dados usando pools elásticos.|
| **Esforço de gerenciamento de desempenho** |canto|Altos|
|**Escala de computação**|Automático|Manual|
|**Capacidade de resposta de computação**|Inferior após períodos inativos|Implantação|
|**Granularidade da cobrança**|Por segundo|Por hora|

## <a name="purchasing-model-and-service-tier"></a>Modelo de compra e camada de serviço

No momento, o banco de dados SQL não tem suporte apenas na camada de Uso Geral no hardware de geração 5 no modelo de compra vCore.

## <a name="autoscaling"></a>Dimensionamento automático

### <a name="scaling-responsiveness"></a>Capacidade de resposta em escala

Em geral, os bancos de dados sem servidor são executados em um computador com capacidade suficiente para satisfazer a demanda de recursos sem interrupção para qualquer quantidade de computação solicitada dentro dos limites definidos pelo valor máximo de vCores. Ocasionalmente, o balanceamento de carga ocorrerá automaticamente se o computador não puder atender à demanda de recursos em alguns minutos. Por exemplo, se a demanda de recursos for 4 vCores, mas apenas 2 vCores estiverem disponíveis, poderá levar alguns minutos para balancear a carga antes de 4 vCores serem fornecidos. O banco de dados permanece online durante o balanceamento de carga, exceto por um breve período no final da operação quando as conexões são descartadas.

### <a name="memory-management"></a>Gerenciamento de memória

A memória para bancos de dados sem servidor é recuperada com mais frequência do que para bancos de dados de computação provisionados. Esse comportamento é importante para controlar os custos sem servidor e pode afetar o desempenho.

#### <a name="cache-reclamation"></a>Recuperação de cache

Ao contrário dos bancos de dados de computação provisionados, a memória do cache SQL é recuperada de um banco de dados sem servidor quando a utilização da CPU ou do cache é baixa.

- A utilização do cache é considerada baixa quando o tamanho total das entradas de cache usadas mais recentemente fica abaixo de um limite por um período de tempo.
- Quando a reclamação do cache é disparada, o tamanho do cache de destino é reduzido incrementalmente para uma fração de seu tamanho anterior e a recuperação só continua se o uso permanecer baixo.
- Quando ocorre a reclamação do cache, a política para selecionar entradas de cache para remover é a mesma política de seleção que para bancos de dados de computação provisionados quando a pressão de memória é alta.
- O tamanho do cache nunca é reduzido abaixo do limite mínimo de memória, conforme definido pelo min vCores, que pode ser configurado.

Em bancos de dados de computação sem servidor e provisionados, as entradas de cache podem ser removidas se toda a memória disponível for usada.

#### <a name="cache-hydration"></a>Hidratação de cache

O cache do SQL cresce à medida que os dados são obtidos do disco da mesma maneira e com a mesma velocidade que para bancos de dados provisionados. Quando o banco de dados está ocupado, o cache tem permissão para aumentar a restrição até o limite máximo de memória.

## <a name="autopausing-and-autoresuming"></a>Pausando e retomando a autopausa

### <a name="autopausing"></a>Pausando

A autopausa será disparada se todas as condições a seguir forem verdadeiras durante o atraso de autopausa:

- Número de sessões = 0
- CPU = 0 para carga de trabalho do usuário em execução no pool de usuários

Uma opção é fornecida para desabilitar a autopausa, se desejado.

Os recursos a seguir não oferecem suporte à autopausa.  Ou seja, se qualquer um dos recursos a seguir for usado, o banco de dados permanecerá online, independentemente da duração da inatividade do banco de dados:

- Replicação geográfica (replicação geográfica ativa e grupos de failover automático).
- Retenção de backup de longo prazo (EPD).
- O banco de dados de sincronização usado no SQL Data Sync.  Ao contrário dos bancos de dados de sincronização, os bancos de dados de Hub e de membros dão suporte à autopausa.
- O banco de dados de trabalho usado em trabalhos elásticos.

A autopausa é temporariamente impedida durante a implantação de algumas atualizações de serviço que exigem que o banco de dados esteja online.  Nesses casos, a pausa automática é permitida novamente após a conclusão da atualização do serviço.

### <a name="autoresuming"></a>Retomada

A retomada será disparada se qualquer uma das seguintes condições for verdadeira a qualquer momento:

|Funcionalidade|Gatilho de retomada|
|---|---|
|Autenticação e autorização|Início de Sessão|
|Deteção de ameaças|Habilitação/desabilitação das configurações de detecção de ameaças no nível do banco de dados ou do servidor.<br>Modificar as configurações de detecção de ameaças no nível do banco de dados ou do servidor.|
|Deteção e classificação de dados|Adicionando, modificando, excluindo ou exibindo rótulos de sensibilidade|
|Auditoria|Exibindo registros de auditoria.<br>Atualizando ou exibindo a política de auditoria.|
|Máscara de dados|Adicionando, modificando, excluindo ou exibindo regras de mascaramento de dados|
|Encriptação de Dados Transparente|Estado de exibição ou status da Transparent Data Encryption|
|Repositório de dados de consulta (desempenho)|Modificando ou exibindo configurações do repositório de consultas|
|Ajuste automática|Aplicativo e verificação de recomendações de ajuste automático, como indexação automática|
|Cópia de banco de dados|Criar banco de dados como cópia.<br>Exportar para um arquivo BACPAC.|
|Sincronização de dados SQL|Sincronização entre bancos de dados de Hub e membro que são executados em um agendamento configurável ou são executados manualmente|
|Modificando determinados metadados de banco de dados|Adicionando novas marcas de banco de dados.<br>Alterando Max vCores, min vCores ou atraso de autopausa.|
|SQL Server Management Studio (SSMS)|O uso de versões do SSMS anteriores a 18,1 e a abertura de uma nova janela de consulta para qualquer banco de dados no servidor retomará qualquer banco de dados pausado automaticamente no mesmo servidor. Esse comportamento não ocorrerá se você estiver usando o SSMS versão 18,1 ou posterior.|

O reinício retomado também é disparado durante a implantação de algumas atualizações de serviço que exigem que o banco de dados esteja online.

### <a name="connectivity"></a>Ligação

Se um banco de dados sem servidor for pausado, o primeiro logon retomará o banco de dados e retornará um erro informando que o banco de dados está indisponível com o código de erro 40613. Depois que o banco de dados for retomado, o logon deverá ser repetido para estabelecer a conectividade. Os clientes de banco de dados com lógica de repetição de conexão não precisam ser modificados.

### <a name="latency"></a>Latência

A latência para retomar e pausar a autopausa em um banco de dados sem servidor geralmente é uma ordem de 1 minuto para retomar e 1-10 minutos para pausar a autopausa.

## <a name="onboarding-into-serverless-compute-tier"></a>Integração na camada de computação sem servidor

A criação de um novo banco de dados ou a movimentação de um banco de dados existente para uma camada de computação sem servidor segue o mesmo padrão que a criação de um novo banco de dados na camada de computação provisionada e envolve as duas etapas a seguir.

1. Especifique o nome do objetivo de serviço. O objetivo do serviço prescreve a camada de serviço, a geração de hardware e o vCores máximo. A tabela a seguir mostra as opções de objetivo de serviço:

   |Nome do objetivo de serviço|Camada de serviços|Geração de hardware|VCores máx.|
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

2. Opcionalmente, especifique o mínimo de vCores e o atraso de autopausa para alterar seus valores padrão. A tabela a seguir mostra os valores disponíveis para esses parâmetros.

   |Parâmetro|Opções de valor|Valor predefinido|
   |---|---|---|---|
   |VCores mín.|Depende do máximo de vCores configurado-consulte [limites de recursos](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).|0,5 vCores|
   |Atraso de autopausa|Mínimo: 60 minutos (1 hora)<br>Máximo: 10080 minutos (7 dias)<br>Incrementos: 60 minutos<br>Desabilitar autopausa:-1|60 minutos|


### <a name="create-new-database-in-serverless-compute-tier"></a>Criar novo banco de dados na camada de computação sem servidor 

#### <a name="use-azure-portal"></a>Utilizar o portal do Azure

Consulte [início rápido: criar um banco de dados individual no banco de dados SQL do Azure usando o portal do Azure](sql-database-single-database-get-started.md).

#### <a name="use-powershell"></a>Utilizar o PowerShell

O exemplo a seguir cria um novo banco de dados na camada de computação sem servidor.  Este exemplo especifica explicitamente o mínimo de vCores, máximo de vCores e atraso de autopausa.

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

#### <a name="use-transact-sql-t-sql"></a>Usar Transact-SQL (T-SQL)

O exemplo a seguir cria um novo banco de dados na camada de computação sem servidor.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Para obter detalhes, consulte [criar banco de dados](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Mover o banco de dados da camada de computação provisionada para a camada de computação sem servidor

#### <a name="use-powershell"></a>Utilizar o PowerShell

O exemplo a seguir move um banco de dados da camada de computação provisionada para a camada de computação sem servidor. Este exemplo especifica explicitamente o mínimo de vCores, máximo de vCores e atraso de autopausa.

```powershell
Set-AzSqlDatabase `
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

#### <a name="use-transact-sql-t-sql"></a>Usar Transact-SQL (T-SQL)

O exemplo a seguir move um banco de dados da camada de computação provisionada para a camada de computação sem servidor. 

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Para obter detalhes, consulte [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Mover o banco de dados da camada de computação sem servidor para a camada de computação provisionada

Um banco de dados sem servidor pode ser movido para uma camada de computação provisionada da mesma maneira que mover um banco de dados de computação provisionado para uma camada de computação sem servidor.

## <a name="modifying-serverless-configuration"></a>Modificando a configuração sem servidor

### <a name="maximum-vcores"></a>Máximo de vCores

#### <a name="use-powershell"></a>Utilizar o PowerShell

Modificar o vCores máximo é executado usando o comando [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) no PowerShell usando o argumento `MaxVcore`.

### <a name="minimum-vcores"></a>Mínimo de vCores

#### <a name="use-powershell"></a>Utilizar o PowerShell

Modificar o vCores mínimo é executado usando o comando [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) no PowerShell usando o argumento `MinVcore`.

### <a name="autopause-delay"></a>Atraso de autopausa

#### <a name="use-powershell"></a>Utilizar o PowerShell

Modificar o atraso de autopausa é executado usando o comando [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) no PowerShell usando o argumento `AutoPauseDelayInMinutes`.

## <a name="monitoring"></a>Monitorização

### <a name="resources-used-and-billed"></a>Recursos usados e cobrados

Os recursos de um banco de dados sem servidor são encapsulados por pacote do aplicativo, instância do SQL e entidades do pool de recursos do usuário.

#### <a name="app-package"></a>Pacote do aplicativo

O pacote do aplicativo é o limite mais externo de gerenciamento de recursos para um banco de dados, independentemente se o banco de dados está em uma camada de computação sem servidor ou provisionada. O pacote do aplicativo contém a instância do SQL e os serviços externos que, juntos, abrangem todos os recursos de usuário e sistema usados por um banco de dados no banco de dados SQL. Exemplos de serviços externos incluem R e pesquisa de texto completo. A instância do SQL geralmente domina a utilização geral de recursos no pacote do aplicativo.

#### <a name="user-resource-pool"></a>Pool de recursos do usuário

O pool de recursos do usuário é o limite de gerenciamento de recursos mais interno para um banco de dados, independentemente de o banco de dados estar em uma camada de computação sem servidor ou provisionada. O pool de recursos do usuário tem como escopo a CPU e a e/s para a carga de trabalho do usuário gerada por consultas DDL, como criar e alterar e consultas DML, como selecionar, inserir, atualizar e excluir. Essas consultas geralmente representam a proporção mais substancial de utilização no pacote do aplicativo.

### <a name="metrics"></a>Métricas

As métricas para monitorar o uso de recursos do pacote do aplicativo e do pool de usuários de um banco de dados sem servidor são listadas na tabela a seguir:

|Entidade|Métrica|Descrição|Unidades|
|---|---|---|---|
|Pacote do aplicativo|app_cpu_percent|Percentual de vCores usado pelo aplicativo em relação ao Max vCores permitido para o aplicativo.|Percentagem|
|Pacote do aplicativo|app_cpu_billed|A quantidade de computação cobrada para o aplicativo durante o período de relatório. O valor pago durante esse período é o produto dessa métrica e o preço unitário vCore. <br><br>Os valores dessa métrica são determinados pela agregação ao longo do tempo o máximo de CPU usado e a memória usada a cada segundo. Se o valor usado for menor que o valor mínimo provisionado conforme definido pelo mínimo de vCores e mín de memória, o valor mínimo provisionado será cobrado. Para comparar a CPU com a memória para fins de cobrança, a memória é normalizada em unidades de vCores, redimensionando a quantidade de memória em GB por 3 GB por vCore.|segundos de vCore|
|Pacote do aplicativo|app_memory_percent|Porcentagem de memória usada pelo aplicativo em relação à memória máxima permitida para o aplicativo.|Percentagem|
|Pool de usuários|cpu_percent|Percentual de vCores usado pela carga de trabalho do usuário em relação ao Max vCores permitido para a carga de trabalho do usuário.|Percentagem|
|Pool de usuários|data_IO_percent|Porcentagem de IOPS de dados usada pela carga de trabalho do usuário em relação ao IOPS de dados máximo permitido para a carga de trabalho do usuário.|Percentagem|
|Pool de usuários|log_IO_percent|Percentual de MB/s de log usado pela carga de trabalho do usuário em relação ao número máximo de MB/s de log permitido para a carga de trabalho do usuário.|Percentagem|
|Pool de usuários|workers_percent|Porcentagem de trabalhadores usados pela carga de trabalho do usuário em relação ao máximo de trabalhadores permitidos para a carga de trabalho do usuário.|Percentagem|
|Pool de usuários|sessions_percent|Porcentagem de sessões usadas pela carga de trabalho do usuário em relação ao máximo de sessões permitidas para a carga de trabalho do usuário.|Percentagem|

### <a name="pause-and-resume-status"></a>Pausar e retomar o status

No portal do Azure, o status do banco de dados é exibido no painel de visão geral do servidor que lista os bancos que ele contém. O status do banco de dados também é exibido no painel Visão geral do banco de dados.

Usando o seguinte comando do PowerShell para consultar o status de pausa e retomada de um banco de dados:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Limites de recursos

Para limites de recursos, consulte [camada de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Faturação

A quantidade de computação cobrada é o máximo usado pela CPU e pela memória usada a cada segundo. Se a quantidade de CPU usada e a memória usada for menor que a quantidade mínima provisionada para cada uma, o valor provisionado será cobrado. Para comparar a CPU com a memória para fins de cobrança, a memória é normalizada em unidades de vCores, redimensionando a quantidade de memória em GB por 3 GB por vCore.

- **Recurso cobrado**: CPU e memória
- **Valor cobrado**: preço unitário vCore * máx (min vCores, vCores usado, min memory gb * 1/3, GB de memória usado * 1/3) 
- **Frequência de cobrança**: por segundo

O preço unitário vCore é o custo por vCore por segundo. Consulte a [página de preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/) para preços unitários específicos em uma determinada região.

A quantidade de computação cobrada é exposta pela seguinte métrica:

- **Métrica**: app_cpu_billed (segundos de vCore)
- **Definição**: Max (min vCores, vCores used, min memory gb * 1/3, memória GB usada * 1/3)
- **Frequência de relatórios**: por minuto

Essa quantidade é calculada a cada segundo e agregada em 1 minuto.

Considere um banco de dados sem servidor configurado com um vCore mínimo de 1 e 4 vCores máximo.  Isso corresponde a cerca de 3 GB de memória e memória máxima de 12 GB.  Suponha que o atraso de pausa automática seja definido como 6 horas e a carga de trabalho do banco de dados esteja ativa durante as duas primeiras horas de um período de 24 horas e, de outra forma, inativa.    

Nesse caso, o banco de dados é cobrado para computação e armazenamento durante as primeiras 8 horas.  Embora o banco de dados esteja inativo iniciando após a segunda hora, ele ainda é cobrado pela computação nas próximas 6 horas com base na computação mínima provisionada enquanto o banco de dados está online.  Somente o armazenamento é cobrado durante o restante do período de 24 horas enquanto o banco de dados é pausado.

Mais precisamente, a fatura de computação neste exemplo é calculada da seguinte maneira:

|Intervalo de tempo|vCores usado por segundo|GB usados a cada segundo|Dimensão de computação cobrada|segundos de vCore cobrados com o intervalo de tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores usado|4 vCores * 3600 segundos = 14400 vCore segundos|
|1:00-2:00|1|12|Memória usada|12 GB * 1/3 * 3600 segundos = 14400 vCore segundos|
|2:00-8:00|0|0|Mín. de memória provisionada|3 GB * 1/3 * 21600 segundos = 21600 vCore segundos|
|8:00-24:00|0|0|Nenhuma computação cobrada durante a pausa|0 segundos vCore|
|Total de segundos vCore cobrados em 24 horas||||50400 segundos de vCore|

Suponha que o preço unitário de computação seja $0.000073/vCore/segundo.  Em seguida, a computação cobrada para esse período de 24 horas é o produto do preço unitário de computação e os segundos de vCore cobrados: $0.000073/vCore/segundo * 50400 vCore segundos = $3.68

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Benefício Híbrido do Azure e capacidade reservada

Benefício Híbrido do Azure (AHB) e os descontos de capacidade reservada não se aplicam à camada de computação sem servidor.

## <a name="available-regions"></a>Regiões disponíveis

A camada de computação sem servidor está disponível em todo o mundo, exceto as seguintes regiões: Leste da China, Norte da China, Alemanha central, Alemanha nordeste, Norte do Reino Unido, Sul do Reino Unido 2, Oeste EUA Central e US Gov central (Iowa).

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [início rápido: criar um banco de dados individual no banco de dados SQL do Azure usando o portal do Azure](sql-database-single-database-get-started.md).
- Para limites de recursos, consulte [limites de recursos da camada de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).

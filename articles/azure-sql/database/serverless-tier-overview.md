---
title: Escalão de serviço de computação sem servidor
description: Este artigo descreve o novo nível de computação sem servidor e compara-o com o nível de computação existente para a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 12/8/2020
ms.openlocfilehash: b0d599b7d52d8a0e93f16761d1983ad25fa45c61
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687404"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL Database sem servidor
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Serverless é um nível de cálculo para bases de dados individuais na Base de Dados Azure SQL que escala automaticamente o cálculo com base na procura de carga de trabalho e contas para a quantidade de cálculo utilizado por segundo. O nível de computação sem servidor também interrompe automaticamente as bases de dados durante períodos inativos quando apenas o armazenamento é faturado e retoma automaticamente as bases de dados quando a atividade retorna.

## <a name="serverless-compute-tier"></a>Escalão de serviço de computação sem servidor

O nível de computação sem servidor para bases de dados individuais na Base de Dados Azure SQL é parametrizado por uma gama de autoscaling computacional e por um atraso de utilização automática. A configuração destes parâmetros molda a experiência de desempenho da base de dados e o custo do cálculo.

![faturação sem servidor](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Configuração de desempenho

- Os **vCores mínimos** e **os vCores máximos** são parâmetros configuráveis que definem a gama de capacidade de computação disponível para a base de dados. Os limites de memória e IO são proporcionais à gama vCore especificada.  
- O **atraso da automatização** é um parâmetro configurável que define o período de tempo em que a base de dados deve estar inativa antes de ser automaticamente interrompida. A base de dados é retomada automaticamente quando ocorre o próximo login ou outra atividade.  Em alternativa, a autopausing pode ser desativada.

### <a name="cost"></a>Cost

- O custo de uma base de dados sem servidor é a soma do custo de cálculo e do custo de armazenamento.
- Quando a utilização do cálculo está entre os limites min e máximo configurados, o custo do cálculo baseia-se no vCore e na memória utilizada.
- Quando a utilização do cálculo está abaixo dos limites min configurados, o custo do cálculo baseia-se no min vCores e na memória min configurada.
- Quando a base de dados é interrompida, o custo do cálculo é zero e apenas os custos de armazenamento são incorridos.
- O custo de armazenagem é determinado da mesma forma que no nível de cálculo previsto.

Para mais detalhes sobre custos, consulte [Billing.](serverless-tier-overview.md#billing)

## <a name="scenarios"></a>Cenários

O escalão Sem servidor está otimizado para uma relação preço/desempenho das bases de dados individuais com padrões de utilização imprevisíveis ou intermitentes que se podem permitir ter algum atraso no aquecimento da computação após períodos de inatividade. Em contrapartida, o escalão de computação aprovisionada está otimizado para uma relação preço/desempenho das bases de dados individuais ou das bases de dados múltiplas em conjuntos elásticos com uma utilização acima da média que não se podem permitir ter nenhum tipo de atraso no aquecimento da computação.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Cenários bem adequados para computação sem servidor

- Bases de dados únicas com padrões de utilização intermitentes e imprevisíveis intercalados com períodos de inatividade e menor utilização média do cálculo ao longo do tempo.
- Bases de dados únicas no nível de computação a provisionado que são frequentemente redimensionados e clientes que preferem delegar o cálculo rescaling ao serviço.
- Novas bases de dados individuais sem histórico de utilização onde o tamanho do cálculo é difícil ou não é possível estimar antes da implementação na Base de Dados SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Cenários adequados para o cálculo a provisionado

- Bases de dados individuais com padrões de utilização mais regulares e previsíveis e uma utilização média mais alta ao longo do tempo.
- Bases de dados que não podem tolerar compensações de desempenho resultantes de aparações de memória mais frequentes ou atrasos na autoresuming de um estado pausado.
- Múltiplas bases de dados com padrões de utilização intermitentes e imprevisíveis que podem ser consolidados em piscinas elásticas para uma melhor otimização do desempenho do preço.

## <a name="comparison-with-provisioned-compute-tier"></a>Comparação com o nível de computação previsto

O quadro que se segue resume as distinções entre o nível de computação sem servidor e o nível de computação previsto:

| | **Computação sem servidor** | **Cálculo provisionado** |
|:---|:---|:---|
|**Padrão de utilização da base de dados**| Uso intermitente e imprevisível com menor utilização de computação média ao longo do tempo. | Padrões de utilização mais regulares com uma utilização média mais alta ao longo do tempo, ou várias bases de dados usando piscinas elásticas.|
| **Esforço de gestão de desempenho** |Mais baixo|Mais alto|
|**Escalagem de cálculo**|Automático|Manual|
|**Capacidade de resposta computacional**|Menor após períodos inativos|Imediato|
|**Granularidade de faturação**|Por segundo|Por hora|

## <a name="purchasing-model-and-service-tier"></a>Modelo de compra e nível de serviço

O sql Database sem servidor é atualmente suportado apenas no nível de Final geral no hardware da Geração 5 no modelo de compra vCore.

## <a name="autoscaling"></a>Dimensionamento automático

### <a name="scaling-responsiveness"></a>Capacidade de resposta de escalonamento

Em geral, as bases de dados sem servidor são executadas numa máquina com capacidade suficiente para satisfazer a procura de recursos sem interrupção por qualquer quantidade de cálculo solicitada dentro dos limites estabelecidos pelo valor máximo vCores. Ocasionalmente, o equilíbrio da carga ocorre automaticamente se a máquina não conseguir satisfazer a procura de recursos dentro de alguns minutos. Por exemplo, se a procura de recursos for de 4 vCores, mas apenas 2 vCores estão disponíveis, então pode levar até alguns minutos para carregar o equilíbrio antes de 4 vCores serem fornecidos. A base de dados permanece on-line durante o equilíbrio de carga, exceto por um breve período no final da operação quando as ligações são largadas.

### <a name="memory-management"></a>Gestão da memória

A memória para bases de dados sem servidor é recuperada com mais frequência do que para bases de dados computacional a provisionadas. Este comportamento é importante para controlar os custos em servidores sem servidor e pode ter impacto no desempenho.

#### <a name="cache-reclamation"></a>Recuperação de cache

Ao contrário das bases de dados de computação a provisionadas, a memória da cache SQL é recuperada a partir de uma base de dados sem servidor quando a CPU ou a utilização ativa da cache são baixas.

- A utilização ativa da cache é considerada baixa quando o tamanho total das entradas de cache mais recentemente utilizadas fica abaixo de um limiar por um período de tempo.
- Quando a recuperação da cache é desencadeada, o tamanho da cache alvo é reduzido gradualmente para uma fração do seu tamanho anterior e a recuperação só continua se o uso permanecer baixo.
- Quando ocorre a recuperação de cache, a política de seleção de entradas de cache para despejar é a mesma política de seleção que para bases de dados de computação a provisionadas quando a pressão da memória é elevada.
- O tamanho da cache nunca é reduzido abaixo do limite de memória min como definido por min vCores que podem ser configurados.

Tanto nas bases de dados de computação sem servidor como nas bases de dados de computação a provisionadas, as entradas em cache podem ser despejadas se for utilizada toda a memória disponível.

Note que quando a utilização do CPU é baixa, a utilização ativa da cache pode permanecer alta dependendo do padrão de utilização e impedir a recuperação da memória.  Além disso, pode haver um atraso adicional após a paragem da atividade do utilizador antes que a recuperação da memória ocorra devido a processos periódicos de fundo que respondem à atividade prévia do utilizador.  Por exemplo, eliminar operações e tarefas de limpeza QDS geram registos fantasma que são marcados para eliminação, mas não são fisicamente eliminados até que o processo de limpeza de fantasmas seja executado que pode envolver a leitura de páginas de dados em cache.

#### <a name="cache-hydration"></a>Hidratação em cache

A cache SQL cresce à medida que os dados são recolhidos do disco da mesma forma e com a mesma velocidade que para as bases de dados a provisionadas. Quando a base de dados está ocupada, a cache é permitida a crescer sem restrições até ao limite máximo de memória.

## <a name="autopausing-and-autoresuming"></a>Autopausing e autoresuming

### <a name="autopausing"></a>Autopausing

A automatização automática é desencadeada se todas as seguintes condições forem verdadeiras durante o atraso da autopausa:

- Sessões de número = 0
- CPU = 0 para a carga de trabalho do utilizador em execução na piscina de utilizadores

É fornecida uma opção para desativar a autopausing se desejar.

As seguintes funcionalidades não suportam a autopausing, mas suportam a auto-escala.  Se alguma das seguintes funcionalidades forem utilizadas, então a utilização automática deve ser desativada e a base de dados permanecerá on-line independentemente da duração da inatividade da base de dados:

- Geo-replicação (geo-replicação ativa e grupos de falha automática).
- Retenção de backup a longo prazo (LTR).
- A base de dados de sincronização utilizada na sincronização de dados SQL.  Ao contrário das bases de dados de sincronização, as bases de dados do hub e dos membros suportam a automatização.
- Pseudónimo do DNS
- A base de dados de trabalho utilizada em Elastic Jobs (pré-visualização).

A utilização automática é temporariamente impedida durante a implementação de algumas atualizações de serviço que exigem que a base de dados esteja online.  Nesses casos, a autopausing torna-se permitida novamente assim que a atualização do serviço estiver concluída.

### <a name="autoresuming"></a>Autoresuming

A autoresuming é desencadeada se alguma das seguintes condições for verdadeira a qualquer momento:

|Funcionalidade|Gatilho autoresume|
|---|---|
|Autenticação e autorização|Iniciar sessão|
|Deteção de ameaças|Ativar/desativar definições de deteção de ameaças no nível da base de dados ou do servidor.<br>Modificar as definições de deteção de ameaças na base de dados ou no nível do servidor.|
|Deteção e classificação de dados|Adicionar, modificar, eliminar ou visualizar rótulos de sensibilidade|
|Auditoria|A ver registos de auditoria.<br>Atualizar ou visualizar a política de auditoria.|
|Máscara de dados|Adicionar, modificar, eliminar ou ver regras de mascaramento de dados|
|Encriptação de dados transparente|Visualização do estado ou estado da encriptação transparente de dados|
|Avaliação de vulnerabilidades|Exames ad hoc e exames periódicos se ativados|
|Loja de dados de consulta (desempenho)|Modificar ou visualizar definições de lojas de consultas|
|Recomendações de desempenho|Visualização ou aplicação de recomendações de desempenho|
|Autofinar|Aplicação e verificação de recomendações de autotuning, tais como auto-indexação|
|Cópia da base de dados|Criar base de dados como cópia.<br>Exportar para um ficheiro BACPAC.|
|Sincronização de dados SQL|Sincronização entre bases de dados do hub e dos membros que funcionam num horário configurável ou são realizadas manualmente|
|Modificação de certos metadados de base de dados|Adicionando novas etiquetas de base de dados.<br>Alterar max vCores, min vCores ou atraso de automatização.|
|SQL Server Management Studio (SSMS)|A utilização de versões SSMS antes de 18.1 e a abertura de uma nova janela de consulta para qualquer base de dados no servidor retomará qualquer base de dados auto-pausada no mesmo servidor. Este comportamento não ocorre se utilizar a versão SSMS 18.1 ou posterior.|

A monitorização, gestão ou outras soluções que efetuem qualquer uma das operações acima enumeradas desencadeará o reinício automático.

A autoresuming também é desencadeada durante a implementação de algumas atualizações de serviço que exigem que a base de dados esteja online.

### <a name="connectivity"></a>Conectividade

Se uma base de dados sem servidor for interrompida, o primeiro login retomará a base de dados e retornará um erro indicando que a base de dados não está disponível com o código de erro 40613. Uma vez retomada a base de dados, o login deve ser novamente testado para estabelecer conectividade. Os clientes da base de dados com lógica de relemisão de ligação não devem ser modificados.

### <a name="latency"></a>Latência

A latência para o autoresume e para a automatização de uma base de dados sem servidor é geralmente ordem de 1 minuto para o autoresume e 1-10 minutos para a automatização.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Encriptação de dados transparente gerida pelo cliente (BYOK)

Se a utilização [da encriptação de dados transparente gerida pelo cliente](transparent-data-encryption-byok-overview.md) (BYOK) e a base de dados sem servidor for pausada automaticamente quando ocorre a eliminação ou revogação da chave, então a base de dados permanece no estado de pausa automática.  Neste caso, após o recomeço da base de dados, a base de dados torna-se inacessível dentro de aproximadamente 10 minutos.  Uma vez que a base de dados se torne inacessível, o processo de recuperação é o mesmo que para bases de dados de computação a provisionadas.  Se a base de dados sem servidor estiver on-line quando ocorre a eliminação ou revogação da chave, então a base de dados também se torna inacessível dentro de aproximadamente 10 minutos, da mesma forma que com bases de dados de computação a provisionadas.

## <a name="onboarding-into-serverless-compute-tier"></a>Embarque no nível de computação sem servidor

Criar uma nova base de dados ou mover uma base de dados existente para um nível de computação sem servidor segue o mesmo padrão que a criação de uma nova base de dados no nível de computação a provisionado e envolve os dois passos seguintes.

1. Especifique o objetivo de serviço. O objetivo do serviço prescreve o nível de serviço, geração de hardware e max vCores. Para opções objetivas de serviço, consulte [os limites de recursos sem servidor](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. Opcionalmente, especifique o min vCores e o atraso de automatização para alterar os seus valores predefinidos. A tabela a seguir mostra os valores disponíveis para estes parâmetros.

   |Parâmetro|Escolhas de valor|Valor predefinido|
   |---|---|---|---|
   |Min vCores|Depende do máximo vCores configurados - ver [limites de recursos](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).|0.5 vCores|
   |Atraso da automatização|Mínimo: 60 minutos (1 hora)<br>Máximo: 10080 minutos (7 dias)<br>Incrementos: 10 minutos<br>Desativar a autopausa: -1|60 minutos|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Criar uma nova base de dados no nível de computação sem servidor

Os exemplos a seguir criam uma nova base de dados no nível de computação sem servidor.

#### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Consulte [Quickstart: Criar uma única base de dados na Base de Dados Azure SQL utilizando o portal Azure](single-database-create-quickstart.md).


#### <a name="use-powershell"></a>Utilizar o PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 --min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Utilizar Transact-SQL (T-SQL)

Ao utilizar o T-SQL, são aplicados valores predefinidos para os min vcores e para o atraso da utilização automática.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Para mais informações, consulte [a BASE DE DADOS CREATE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Mover uma base de dados do nível de computação provisionado para o nível de computação sem servidor

Os exemplos seguintes movem uma base de dados do nível de computação provisionado para o nível de computação sem servidor.

#### <a name="use-powershell"></a>Utilizar o PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Utilizar Transact-SQL (T-SQL)

Ao utilizar o T-SQL, são aplicados valores predefinidos para os min vcores e para o atraso da utilização automática.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Para mais informações, consulte [a ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Mover uma base de dados do nível de computação sem servidor para o nível de computação provisionado

Uma base de dados sem servidor pode ser transferida para um nível de computação a provisionado da mesma forma que mover uma base de dados de computação a provisionada para um nível de computação sem servidor.

## <a name="modifying-serverless-configuration"></a>Modificar a configuração sem servidor

### <a name="use-powershell"></a>Utilizar o PowerShell

Modificar os vCores máximos ou mínimos e o atraso de automatização é realizado utilizando o comando [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) em PowerShell utilizando o `MaxVcore` , e os `MinVcore` `AutoPauseDelayInMinutes` argumentos.

### <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Modificar os vCores máximos ou mínimos e o atraso de automatização é realizado utilizando o comando [de atualização az sql db](/cli/azure/sql/db#az-sql-db-update) em Azure CLI utilizando o `capacity` , e `min-capacity` `auto-pause-delay` argumentos.


## <a name="monitoring"></a>Monitorização

### <a name="resources-used-and-billed"></a>Recursos utilizados e faturados

Os recursos de uma base de dados sem servidor são encapsulados por pacote de aplicações, instância SQL e entidades de pool de recursos de utilizador.

#### <a name="app-package"></a>Pacote de aplicações

O pacote de aplicações é o limite de gestão de recursos mais externo para uma base de dados, independentemente de a base de dados estar num nível de computação sem servidor ou aprovisionado. O pacote de aplicações contém a instância SQL e os serviços externos que, em conjunto, aplicam todos os recursos do utilizador e do sistema utilizados por uma base de dados na Base de Dados SQL. Exemplos de serviços externos incluem R e pesquisa de texto completo. A instância SQL geralmente domina a utilização global de recursos em todo o pacote de aplicações.

#### <a name="user-resource-pool"></a>Piscina de recursos do utilizador

O conjunto de recursos do utilizador é o limite de gestão de recursos mais interno para uma base de dados, independentemente de a base de dados estar num nível de computação sem servidor ou aprovisionado. O conjunto de recursos do utilizador aplica CPU e IO para a carga de trabalho do utilizador gerada por consultas DDL tais como consultas CREATE e ALTER e DML tais como SELECT, INSERT, UPDATE e DELETE. Estas consultas geralmente representam a proporção mais substancial de utilização dentro do pacote de aplicações.

### <a name="metrics"></a>Métricas

As métricas para monitorizar a utilização de recursos do pacote de aplicações e o conjunto de utilizadores de uma base de dados sem servidor estão listadas na seguinte tabela:

|Entidade|Metric|Descrição|Unidades|
|---|---|---|---|
|Pacote de aplicações|app_cpu_percent|Percentagem de vCores utilizados pela app relativa ao max vCores permitido para a aplicação.|Percentagem|
|Pacote de aplicações|app_cpu_billed|A quantidade de cálculo faturada para a app durante o período de reporte. O valor pago durante este período é o produto desta métrica e do preço unitário vCore. <br><br>Os valores desta métrica são determinados agregando ao longo do tempo o máximo de CPU utilizado e a memória utilizada a cada segundo. Se o montante utilizado for inferior ao montante mínimo previsto pela memória min vCores e min, então o montante mínimo previsto é faturado.Para comparar o CPU com a memória para efeitos de faturação, a memória é normalizada em unidades de vCores redimensionando a quantidade de memória em GB por 3 GB por vCore.|vCore segundos|
|Pacote de aplicações|app_memory_percent|Percentagem de memória utilizada pela app relativa à memória máxima permitida para a aplicação.|Percentagem|
|Piscina de utilizadores|cpu_percent|Percentagem de vCores utilizados pela carga de trabalho do utilizador relativamente ao máximo vCores permitidos para a carga de trabalho do utilizador.|Percentagem|
|Piscina de utilizadores|data_IO_percent|Percentagem de dados IOPS utilizados pela carga de trabalho do utilizador em relação aos dados máximos que o IOPS permitiu para a carga de trabalho do utilizador.|Percentagem|
|Piscina de utilizadores|log_IO_percent|Percentagem de registo MB/s utilizado pela carga de trabalho do utilizador relativamente ao registo máximo MB/s permitido para a carga de trabalho do utilizador.|Percentagem|
|Piscina de utilizadores|workers_percent|Percentagem de trabalhadores utilizados pela carga de trabalho dos utilizadores relativamente aos trabalhadores máximos permitidos para a carga de trabalho dos utilizadores.|Percentagem|
|Piscina de utilizadores|sessions_percent|Percentagem de sessões utilizadas pela carga de trabalho do utilizador em relação às sessões máximas permitidas para a carga de trabalho do utilizador.|Percentagem|

### <a name="pause-and-resume-status"></a>Pausa e retomar estado

No portal Azure, o estado da base de dados é apresentado no painel de visão geral do servidor que lista as bases de dados que contém. O estado da base de dados também é apresentado no painel geral da base de dados.

Utilizando os seguintes comandos para consultar a pausa e retomar o estado de uma base de dados:

#### <a name="use-powershell"></a>Utilizar o PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Limites de recursos

Para obter limites de recursos, consulte [o nível de cálculo sem servidor](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Faturação

A quantidade de cálculo faturada é o máximo de CPU utilizado e a memória utilizada a cada segundo. Se a quantidade de CPU utilizada e a memória utilizada for inferior ao montante mínimo previsto para cada um, então o montante previsto é faturado. Para comparar o CPU com a memória para efeitos de faturação, a memória é normalizada em unidades de vCores redimensionando a quantidade de memória em GB por 3 GB por vCore.

- **Recurso faturado**: CPU e memória
- **Valor faturado**: vCore preço unitário * max (min vCores, vCores usados, min memory GB * 1/3, memória GB usada * 1/3) 
- **Frequência de faturação**: Por segundo

O preço unitário vCore é o custo por vCore por segundo. Consulte a página de preços da [Base de Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/) para obter preços unitários específicos numa determinada região.

A quantidade de cálculo faturada é exposta pela seguinte métrica:

- **Métrica**: app_cpu_billed (vCore segundos)
- **Definição:** máx (min vCores, vCores usados, memória min GB * 1/3, memória GB usada * 1/3)
- **Frequência de reporte**: Por minuto

Esta quantidade é calculada a cada segundo e agregada ao longo de 1 minuto.

### <a name="minimum-compute-bill"></a>Conta de computação mínima

Se uma base de dados sem servidor for pausada, a conta de cálculo é zero.  Se uma base de dados sem servidor não for pausada, então a conta de cálculo mínima não é inferior à quantidade de vCores com base no máximo (min vCores, memória min GB * 1/3).

Exemplos:

- Suponha que uma base de dados sem servidor não seja pausada e configurada com 8 vCores máximos e 1 min vCore correspondente a 3,0 GB de memória.  Em seguida, a conta mínima de cálculo baseia-se no máximo (1 vCore, 3.0 GB * 1 vCore / 3 GB) = 1 vCore.
- Suponha que uma base de dados sem servidor não seja pausada e configurada com 4 vCores máximos e 0,5 min vCores correspondentes a 2,1 GB de memória min.  Em seguida, a conta mínima de cálculo baseia-se no máximo (0,5 vCores, 2.1 GB * 1 vCore / 3 GB) = 0,7 vCores.

A [calculadora de preços da Base de Dados Azure SQL](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para sem servidor pode ser utilizada para determinar a memória min configurável com base no número de max e min vCores configurados.  Em regra, se o min vCores configurado for superior a 0.5 vCores, então a conta de cálculo mínima é independente da memória min configurada e baseada apenas no número de min vCores configurados.

### <a name="example-scenario"></a>Cenário de exemplo

Considere uma base de dados sem servidor configurada com 1 min vCore e 4 max vCores.  Isto corresponde a cerca de 3 GB de memória e memória máxima de 12 GB.  Suponha que o atraso de pausa automática esteja definido para 6 horas e a carga de trabalho da base de dados esteja ativa durante as primeiras 2 horas de um período de 24 horas e de outra forma inativa.    

Neste caso, a base de dados é faturada para computação e armazenamento durante as primeiras 8 horas.  Apesar de a base de dados estar inativa a partir da segunda hora, ainda é faturada para o cálculo nas 6 horas seguintes com base no cálculo mínimo previsto enquanto a base de dados está online.  Apenas o armazenamento é faturado durante o resto do período de 24 horas enquanto a base de dados é interrompida.

Mais precisamente, a conta de cálculo neste exemplo é calculada da seguinte forma:

|Intervalo de tempo|vCores usados a cada segundo|GB usado a cada segundo|Dimensão computacional faturada|vCore segundos faturados ao longo do intervalo de tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores usados|4 vCores * 3600 segundos = 14400 vCore segundos|
|1:00-2:00|1|12|Memória utilizada|12 GB * 1/3 * 3600 segundos = 14400 vCore segundos|
|2:00-8:00|0|0|Memória min a provisionada|3 GB * 1/3 * 21600 segundos = 21600 vCore segundos|
|8:00-24:00|0|0|Sem cálculo faturado durante a pausa|0 vCore segundos|
|Total de segundos vCore faturados ao longo de 24 horas||||50400 vCore segundos|

Suponha que o preço unitário do cálculo seja $0.000145/vCore/segundo.  Em seguida, o cálculo faturado para este período de 24 horas é o produto do preço unitário do cálculo e dos segundos vCore cobrados: $0.000145/vCore/segundo * 50400 vCore segundos ~ $7,31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Benefício Híbrido Azure e capacidade reservada

O Azure Hybrid Benefit (AHB) e os descontos de capacidade reservados não se aplicam ao nível de computação sem servidor.

## <a name="available-regions"></a>Regiões disponíveis

O nível de computação sem servidores está disponível em todo o mundo, exceto as seguintes regiões: China East, China North, Germany Central, Germany Northeast, e US Gov Central (Iowa).

## <a name="next-steps"></a>Próximos passos

- Para começar, consulte [Quickstart: Crie uma única base de dados na Base de Dados Azure SQL utilizando o portal Azure](single-database-create-quickstart.md).
- Para obter limites de recursos, consulte [os limites de recursos de nível de cálculo serverless](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).

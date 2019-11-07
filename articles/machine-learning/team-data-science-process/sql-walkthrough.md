---
title: Criar e implantar um modelo em um SQL Server VM-processo de ciência de dados da equipe
description: Crie e implante um modelo de aprendizado de máquina usando SQL Server em uma VM do Azure com um conjunto de informações publicamente disponível.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 148d0c203248e4dcde5baaadc596d56e8b8ea17a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669386"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>O processo de ciência de dados de equipe em ação: usando SQL Server
Neste tutorial, você percorre o processo de criação e implantação de um modelo de aprendizado de máquina usando SQL Server e um conjunto de informações publicamente disponível – o conjunto de informações de [táxis de NYC](https://www.andresmh.com/nyctaxitrips/) . O procedimento segue um fluxo de trabalho de ciência de dados padrão: ingerir e explorar os dados, recursos de engenharia para facilitar o aprendizado e, em seguida, criar e implantar um modelo.

## <a name="dataset"></a>Descrição do conjunto de NYC de viagens de táxi
Os dados de corrida de táxi de NYC são cerca de 20 GB de arquivos CSV compactados (~ 48 GB descompactados), que abrangem mais de 173 milhões viagens individuais e as tarifas pagas por cada viagem. Cada registro de corrida inclui o local e o horário de retirada e chegada, o número de licença de hack (motorista) anônimo e o número de Medallion (ID exclusiva do táxi). Os dados abrangem todas as viagens no ano de 2013 e são fornecidos nos dois conjuntos a seguir para cada mês:

1. O CSV ' trip_data ' contém detalhes da corrida, como o número de passageiros, pontos de retirada e chegada, duração da corrida e duração da viagem. Aqui estão alguns registros de exemplo:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. O CSV ' trip_fare ' contém detalhes da tarifa paga para cada corrida, como tipo de pagamento, valor da tarifa, sobretaxa e impostos, dicas e tarifas e o valor total pago. Aqui estão alguns registros de exemplo:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para unir\_dados e viagens\_tarifa é composta pelos campos: Medallion, ataque\_licença e retirada\_DateTime.

## <a name="mltasks"></a>Exemplos de tarefas de previsão
Formularemos três problemas de previsão com base na *\_valor da gorjeta*, ou seja:

1. Classificação binária: prever se uma gorjeta foi paga ou não por uma corrida, ou seja, uma *dica\_valor* maior que $0 é um exemplo positivo, enquanto uma *dica\_valor* de $0 é um exemplo negativo.
2. Classificação multiclasse: prever o intervalo de gorjetas pagas para a viagem. Dividimos a *dica\_valor* em cinco compartimentos ou classes:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Tarefa de regressão: prever a quantidade de gorjeta paga por uma corrida.  

## <a name="setup"></a>Configurando o ambiente de ciência de dados do Azure para análise avançada
Como você pode ver no guia [planejar seu ambiente](plan-your-environment.md) , há várias opções para trabalhar com o conjunto de NYC de viagens de táxi no Azure:

* Trabalhe com os dados em BLOBs do Azure e, em seguida, modele em Azure Machine Learning
* Carregue os dados em um banco de dado SQL Server e, em seguida, modele em Azure Machine Learning

Neste tutorial, demonstraremos a importação em massa paralela dos dados para uma SQL Server, exploração de dados, engenharia de recursos e amostragem vertical usando SQL Server Management Studio, bem como usando o notebook IPython. [Scripts de exemplo](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) e [notebooks ipython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) são compartilhados no github. Um notebook IPython de exemplo para trabalhar com os dados em BLOBs do Azure também está disponível no mesmo local.

Para configurar seu ambiente de ciência de dados do Azure:

1. [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md)
2. [Criar um espaço de trabalho Azure Machine Learning](../studio/create-workspace.md)
3. [Provisionar um máquina virtual de ciência de dados](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), que fornece um SQL Server e um servidor do ipython notebook.
   
   > [!NOTE]
   > Os scripts de exemplo e os blocos de anotações do IPython serão baixados para sua máquina virtual de ciência de dados durante o processo de instalação. Quando o script pós-instalação da VM for concluído, os exemplos estarão na biblioteca de documentos da VM:  
   > 
   > * Scripts de exemplo: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Blocos de anotações do IPython de exemplo: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   em que `<user_name>` é o nome de logon do Windows da VM. Vamos nos referir às pastas de exemplo como **scripts de exemplo** e **notebooks ipython de exemplo**.
   > 
   > 

Com base no tamanho do conjunto de dados, no local da fonte de dado e no ambiente de destino do Azure selecionado, esse cenário é semelhante ao [cenário \#5: conjunto de dados grande em arquivos locais, SQL Server de destino na VM do Azure](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Obter os dados da fonte pública
Para obter o conjunto de dados de corridas de [táxi de NYC](https://www.andresmh.com/nyctaxitrips/) de seu local público, você pode usar qualquer um dos métodos descritos em mover os dados de e para o [armazenamento de BLOBs do Azure](move-azure-blob.md) para copiar os dados para a nova máquina virtual.

Para copiar os dados usando AzCopy:

1. Faça logon em sua VM (máquina virtual)
2. Crie um novo diretório no disco de dados da VM (Observação: não use o disco temporário que vem com a VM como um disco de dados).
3. Em uma janela de prompt de comando, execute a seguinte linha de comando Azcopy, substituindo < path_to_data_folder > pela pasta de dados criada em (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Quando o AzCopy é concluído, um total de 24 arquivos CSV compactados (12 para viagens\_dados e 12 para viagens\_Tarifa) devem estar na pasta de dados.
4. Descompacte os arquivos baixados. Observe a pasta onde residem os arquivos descompactados. Essa pasta será referida como o caminho de <\_para\_arquivos de\_de dados\>.

## <a name="dbload"></a>Importar dados em massa para o SQL Server Database
O desempenho de carregar/transferir grandes quantidades de dados para um banco de dados SQL e consultas subsequentes pode ser melhorado usando *tabelas e exibições particionadas*. Nesta seção, seguiremos as instruções descritas em [importação de dados em massa em paralelo usando tabelas de partição do SQL](parallel-load-sql-partitioned-tables.md) para criar um novo banco de dados e carregá-los em tabelas particionadas em paralelo.

1. Enquanto estiver conectado à sua VM, inicie o **SQL Server Management Studio**.
2. Conecte-se usando a autenticação do Windows.
   
    ![Conexão do SSMS][12]
3. Se você ainda não alterou o modo de autenticação SQL Server e criou um novo usuário de logon do SQL, abra o arquivo de script chamado **change\_auth. SQL** na pasta **scripts de exemplo** . Altere o nome de usuário e a senha padrão. Clique em **! Execute** na barra de ferramentas para executar o script.
   
    ![Executar script][13]
4. Verifique e/ou altere o SQL Server o banco de dados padrão e as pastas de log para garantir que os bancos de dado recém-criados serão armazenados em um disco. A imagem de VM SQL Server que é otimizada para cargas de armazenamento de dados é pré-configurada com discos de log e data. Se sua VM não incluía um disco de dados e você adicionou novos discos rígidos virtuais durante o processo de configuração da VM, altere as pastas padrão da seguinte maneira:
   
   * Clique com o botão direito do mouse no nome do SQL Server no painel esquerdo e clique em **Propriedades**.
     
       ![Propriedades de SQL Server][14]
   * Selecione **configurações de banco de dados** na lista **selecionar uma página** à esquerda.
   * Verifique e/ou altere os **locais padrão do banco** de **dados** para as localizações de disco da sua escolha. É aí que os novos bancos de dados residem se forem criados com as configurações de local padrão.
     
       ![Padrões do banco de dados SQL][15]  
5. Para criar um novo banco de dados e um conjunto de grupos de filepara conter as tabelas particionadas, abra o script de exemplo **criar\_db\_default. SQL**. O script criará um novo banco de dados chamado **TaxiNYC** e 12 grupos de File, no local de dado padrão. Cada grupo de arquivos conterá um mês de viagem\_dados e viagens\_dados de Tarifa. Modifique o nome do banco de dados, se desejar. Clique em **! Execute** para executar o script.
6. Em seguida, crie duas tabelas de partição, uma para a viagem\_dados e outra para a viagem\_tarifa. Abra o script de exemplo **create\_particionado\_Table. SQL**, que irá:
   
   * Crie uma função de partição para dividir os dados por mês.
   * Crie um esquema de partição para mapear os dados de cada mês para um grupo de arquivos diferente.
   * Crie duas tabelas particionadas mapeadas para o esquema de partição: **nyctaxi\_Trip** manterá a viagem\_dados e **nyctaxi\_Tarifa** conterá a corrida\_dados de tarifas.
     
     Clique em **! Execute** para executar o script e criar as tabelas particionadas.
7. Na pasta de **scripts de exemplo** , há dois scripts de exemplo do PowerShell fornecidos para demonstrar importações em massa paralelas de dados para SQL Server tabelas.
   
   * o **bcp\_paralelo\_Generic. ps1** é um script genérico para importar dados em massa em paralelo em uma tabela. Modifique esse script para definir as variáveis de entrada e de destino conforme indicado nas linhas de comentário no script.
   * o **bcp\_parallel\_nyctaxi. ps1** é uma versão pré-configurada do script genérico e pode ser usado para carregar ambas as tabelas para os dados de corridas de táxi de NYC.  
8. Clique com o botão direito do mouse no nome do script **bcp\_parallel\_nyctaxi. ps1** e clique em **Editar** para abri-lo no PowerShell. Revise as variáveis predefinidas e modifique de acordo com o nome do banco de dados selecionado, a pasta Data de entrada, a pasta de log de destino e os caminhos para os arquivos de formato de exemplo **nyctaxi_trip. xml** e **nyctaxi\_tarifa. xml** (fornecido nos **scripts de exemplo** pasta).
   
    ![Importar dados em massa][16]
   
    Você também pode selecionar o modo de autenticação, o padrão é autenticação do Windows. Clique na seta verde na barra de ferramentas para executar. O script iniciará 24 operações de importação em massa em paralelo, 12 para cada tabela particionada. Você pode monitorar o progresso da importação de dados abrindo a SQL Server pasta de dados padrão conforme definido acima.
9. O script do PowerShell relata os horários de início e término. Quando todas as importações em massa forem concluídas, a hora de término será relatada. Verifique a pasta de log de destino para verificar se as importações em massa foram bem-sucedidas, ou seja, se não há erros relatados na pasta de log de destino.
10. Seu banco de dados agora está pronto para exploração, engenharia de recursos e outras operações, conforme desejado. Como as tabelas são particionadas de acordo com o campo **\_DateTime de retirada** , as consultas que incluem **retirada\_condições DateTime** na cláusula **Where** se beneficiarão do esquema de partição.
11. Em **SQL Server Management Studio**, explore o exemplo de script de exemplo fornecido **\_consultas. SQL**. Para executar qualquer uma das consultas de exemplo, realce as linhas de consulta e clique em **! Execute** na barra de ferramentas.
12. Os dados de viagens de táxi de NYC são carregados em duas tabelas separadas. Para melhorar as operações de junção, é altamente recomendável indexá-las. O script de exemplo **criar\_particionado\_index. o SQL** cria índices particionados na chave de junção composta **medallion, hack\_license e pickup\_DateTime**.

## <a name="dbexplore"></a>Exploração de dados e engenharia de recursos no SQL Server
Nesta seção, executaremos a exploração de dados e a geração de recursos executando consultas SQL diretamente no **SQL Server Management Studio** usando o banco de SQL Server o dados criado anteriormente. Um script de exemplo chamado **sample\_consulta. o SQL** é fornecido na pasta de **scripts de exemplo** . Modifique o script para alterar o nome do banco de dados, se ele for diferente do padrão: **TaxiNYC**.

Neste exercício, iremos:

* Conecte-se a **SQL Server Management Studio** usando a autenticação do Windows ou a autenticação do SQL e o nome de logon e a senha do SQL.
* Explore as distribuições de dados de alguns campos em janelas de tempo variáveis.
* Investigue a qualidade dos dados dos campos de longitude e latitude.
* Gere rótulos de classificação binária e multiclasse com base na **\_valor da dica**.
* Gerar recursos e computar/comparar distâncias de viagem.
* Junte as duas tabelas e extraia uma amostra aleatória que será usada para criar modelos.

Quando estiver pronto para continuar a Azure Machine Learning, você pode:  

1. Salve a consulta SQL final para extrair e obter amostras dos dados e copiar e colar a consulta diretamente em um módulo [importar dados][import-data] no Azure Machine Learning ou
2. Persista os dados de amostra e de engenharia que você planeja usar para a criação de modelos em uma nova tabela de banco de dados e use a nova tabela no módulo [importar data][import-data] no Azure Machine Learning.

Nesta seção, salvaremos a consulta final para extrair e obter amostras dos dados. O segundo método é demonstrado na seção [exploração de dados e engenharia de recursos no ipython Notebook](#ipnb) .

Para uma verificação rápida do número de linhas e colunas nas tabelas preenchidas anteriormente usando a importação em massa paralela,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: distribuição de viagens por Medallion
Este exemplo identifica o Medallion (números de táxi) com mais de 100 corridas em um determinado período de tempo. A consulta se beneficiaria do acesso à tabela particionada, pois ela é condicional pelo esquema de partição de **retirada\_DateTime**. Consultar o conjunto de um inteiro também fará uso da tabela particionada e/ou da verificação de índice.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploração: distribuição de viagens por Medallion e hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação de qualidade de dados: verificar registros com longitude e/ou latitude incorretos
Este exemplo investiga se qualquer um dos campos de longitude e/ou latitude contém um valor inválido (os graus radianos devem estar entre-90 e 90) ou têm coordenadas (0, 0).

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: distribuição de viagens gorjetas versus não inclinadas
Este exemplo localiza o número de corridas que foram arestadas versus não inclinadas em um determinado período de tempo (ou no conjunto de dados completo se abrangendo o ano inteiro). Essa distribuição reflete que a distribuição de rótulo binário será usada posteriormente para a modelagem de classificação binária.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Exploração: distribuição de classe/intervalo de gorjeta
Este exemplo computa a distribuição de intervalos de gorjeta em um determinado período de tempo (ou no conjunto de dados completo, se abrangendo o ano inteiro). Esta é a distribuição das classes de rótulo que serão usadas posteriormente para a modelagem de classificação multiclasse.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: computação e comparação da distância de viagem
Este exemplo converte os pontos geográficos de retirada e chegada de longitude e latitude em SQL, computa a distância de corrida usando a diferença de pontos geográficos do SQL e retorna uma amostra aleatória dos resultados para comparação. O exemplo limita os resultados a coordenadas válidas usando a consulta de avaliação de qualidade de dados abordada anteriormente.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Engenharia de recursos em consultas SQL
A geração de rótulo e as consultas de exploração de conversão de geografia também podem ser usadas para gerar rótulos/recursos removendo a parte de contagem. Exemplos adicionais de engenharia de recursos SQL são fornecidos na seção [exploração de dados e engenharia de recursos na ipython Notebook](#ipnb) . É mais eficiente executar as consultas de geração de recursos no conjunto de dados completo ou em um grande subconjunto dela usando consultas SQL que são executadas diretamente na instância do banco de dados SQL Server. As consultas podem ser executadas em **SQL Server Management Studio**, Notebook ipython ou qualquer ferramenta de desenvolvimento/ambiente que possa acessar o banco de dados localmente ou remotamente.

#### <a name="preparing-data-for-model-building"></a>Preparando dados para construção de modelo
A consulta a seguir une o **nyctaxi\_Trip** e o **nyctaxi\_** tabelas de tarifas, gera um rótulo de classificação binária **inclinado**, um rótulo de classificação multiclasse **Tip\_classe**e extrai um 1% de modo aleatório exemplo do conjunto de associação completo. Essa consulta pode ser copiada e colada diretamente no módulo [Azure Machine Learning Studio](https://studio.azureml.net) [importar dados][import-data] para a ingestão direta de dados da instância de banco de SQL Server no Azure. A consulta exclui registros com coordenadas incorretas (0, 0).

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Exploração de dados e engenharia de recursos no IPython Notebook
Nesta seção, realizaremos a exploração de dados e a geração de recursos usando consultas do Python e do SQL com base no banco de dado SQL Server criado anteriormente. Um notebook IPython de exemplo chamado **Machine-Learning-data-Ciências-Process-SQL-Story. ipynb** é fornecido na pasta de **exemplo do ipython notebooks** . Este notebook também está disponível no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

A sequência recomendada ao trabalhar com Big Data é a seguinte:

* Leia em uma pequena amostra dos dados em um quadro de dados na memória.
* Execute algumas visualizações e explorações usando os dados de amostra.
* Experimente a engenharia de recursos usando os dados de amostra.
* Para uma exploração de dados maior, a manipulação de dados e a engenharia de recursos, use o Python para emitir consultas SQL diretamente em relação ao banco de SQL Server na VM do Azure.
* Decida o tamanho da amostra a ser usado para Azure Machine Learning compilação do modelo.

Quando estiver pronto para continuar a Azure Machine Learning, você pode:  

1. Salve a consulta SQL final para extrair e obter amostras dos dados e copiar e colar a consulta diretamente em um módulo [importar dados][import-data] no Azure Machine Learning. Esse método é demonstrado na seção [criando modelos no Azure Machine Learning](#mlmodel) .    
2. Persista os dados de amostra e projetados que você planeja usar para a criação de modelos em uma nova tabela de banco de dados e, em seguida, use a nova tabela no módulo [importar data][import-data] .

A seguir estão alguns exemplos de exploração de dados, visualização de dados e engenharia de recursos. Para obter mais exemplos, consulte o notebook SQL IPython de exemplo na pasta **ipython notebooks de exemplo** .

#### <a name="initialize-database-credentials"></a>Inicializar credenciais do banco de dados
Inicialize as configurações de conexão do banco de dados nas seguintes variáveis:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Criar conexão de banco de dados
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Relatar o número de linhas e colunas na tabela nyctaxi_trip
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Número total de linhas = 173179759  
* Número total de colunas = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Leia-em uma amostra de dados pequena do banco de dado SQL Server
    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

O tempo para ler a tabela de exemplo é de 6,492000 segundos  
Número de linhas e colunas recuperadas = (84952, 21)

#### <a name="descriptive-statistics"></a>Estatísticas descritivas
Agora, você está pronto para explorar os dados de amostra. Começamos examinando as estatísticas descritivas para o **percurso\_distância** (ou qualquer outro campo):

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualização: exemplo de plotagem de caixa
Em seguida, vamos examinar a caixa para a distância da corrida para visualizar o quantis

    df1.boxplot(column='trip_distance',return_type='dict')

![#1 de plotagem][1]

#### <a name="visualization-distribution-plot-example"></a>Visualização: exemplo de plotagem de distribuição
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![#2 de plotagem][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualização: plotagens de barras e linhas
Neste exemplo, nós colocamos a distância da corrida em cinco compartimentos e visualizamos os resultados do compartimentalização.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos plotar a distribuição de bin acima em uma barra ou gráfico de linhas como abaixo

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![#3 de plotagem][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![#4 de plotagem][4]

#### <a name="visualization-scatterplot-example"></a>Visualização: exemplo de dispersão
Mostramos gráficos de dispersão entre **viagens\_\_de tempo em\_s** e **viagem\_distância** para ver se há alguma correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![#6 de plotagem][6]

Da mesma forma, podemos verificar a relação entre o **código de\_de taxa** e a **viagem\_distância**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![#8 de plotagem][8]

### <a name="sub-sampling-the-data-in-sql"></a>Subamostrando os dados no SQL
Ao preparar dados para a criação de modelos no [Azure Machine Learning Studio](https://studio.azureml.net), você pode decidir sobre a **consulta SQL a ser usada diretamente no módulo importar dados** ou manter os dados com engenharia e amostra em uma nova tabela, que pode ser usada na [importação ][import-data]Módulo de dados com um simples **Select * de < sua\_nova tabela de\_\_nome >** .

Nesta seção, criaremos uma nova tabela para manter os dados de amostra e de engenharia. Um exemplo de uma consulta SQL direta para a criação de modelos é fornecido na seção [exploração de dados e engenharia de recursos na SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Crie uma tabela de exemplo e preencha com 1% das tabelas unidas. Remover tabela primeiro se ela existir.
Nesta seção, juntamos as tabelas **nyctaxi\_Trip** e **nyctaxi\_Tarifa**, extração de um exemplo aleatório de 1% e persistem os dados de amostra em um novo nome de tabela **nyctaxi\_um\_%** :

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Exploração de dados usando consultas SQL no notebook IPython
Nesta seção, exploraremos distribuições de dados usando os dados de amostra 1% que são persistidos na nova tabela que criamos acima. Observe que explorações semelhantes podem ser executadas usando as tabelas originais, opcionalmente usando **TABLESAMPLE** para limitar o exemplo de exploração ou limitando os resultados a um determinado período de tempo usando a **retirada\_partições DateTime** , como ilustrado na seção [exploração de dados e engenharia de recursos na SQL Server](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: distribuição diária de viagens
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: distribuição de viagens por Medallion
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Geração de recursos usando consultas SQL no notebook IPython
Nesta seção, geraremos novos rótulos e recursos diretamente usando consultas SQL, operando na tabela de exemplo 1% criada na seção anterior.

#### <a name="label-generation-generate-class-labels"></a>Geração de rótulo: gerar rótulos de classe
No exemplo a seguir, geramos dois conjuntos de rótulos a serem usados para modelagem:

1. Rótulos de classes binárias **inclinados** (prevendo se uma gorjeta será fornecida)
2. Rótulos multiclasse **tip\_classe** (prevendo o compartimento ou o intervalo de gorjeta)
   
        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''
   
        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()
   
        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''
   
        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Engenharia de recursos: contar recursos para colunas categóricas
Este exemplo transforma um campo categórico em um campo numérico substituindo cada categoria pela contagem de suas ocorrências nos dados.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Engenharia de recursos: recursos de compartimento para colunas numéricas
Este exemplo transforma um campo numérico contínuo em intervalos de categoria predefinidos, ou seja, transforma o campo numérico em um campo categórico.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Engenharia de recursos: extrair recursos de localização de latitude/longitude decimal
Este exemplo divide a representação decimal de um campo de latitude e/ou longitude em vários campos de região de granularidade diferente, como país/região, cidade, cidade, bloco, etc. Observe que os novos campos geográficos não são mapeados para locais reais. Para obter informações sobre como mapear locais geocódigo, consulte [serviços REST do Bing Maps](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifique a forma final da tabela destacados
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Agora estamos prontos para continuar a criação de modelos e implantação de modelo no [Azure Machine Learning](https://studio.azureml.net). Os dados estão prontos para qualquer um dos problemas de previsão identificados anteriormente, isto é:

1. Classificação binária: para prever se uma gorjeta foi paga ou não por uma viagem.
2. Classificação multiclasse: prever o intervalo de gorjetas pagas, de acordo com as classes definidas anteriormente.
3. Tarefa de regressão: prever a quantidade de gorjeta paga por uma corrida.  

## <a name="mlmodel"></a>Criando modelos no Azure Machine Learning
Para iniciar o exercício de modelagem, faça logon no seu espaço de trabalho do Azure Machine Learning. Se você ainda não criou um espaço de trabalho do Machine Learning, consulte [criar um Azure Machine Learning espaço de trabalho](../studio/create-workspace.md).

1. Para começar a usar o Azure Machine Learning, consulte [o que é Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Faça logon no [Azure Machine Learning Studio](https://studio.azureml.net).
3. A home page do estúdio fornece uma grande quantidade de informações, vídeos, tutoriais, links para a referência de módulos e outros recursos. Para obter mais informações sobre Azure Machine Learning, consulte o [Azure Machine Learning centro de documentação](https://azure.microsoft.com/documentation/services/machine-learning/).

Um experimento de treinamento típico consiste no seguinte:

1. Crie um experimento **+ novo** .
2. Obtenha os dados para Azure Machine Learning.
3. Pré-processe, transforme e manipule os dados conforme necessário.
4. Gere recursos conforme necessário.
5. Divida os dados em conjuntos de dado de treinamento/validação/teste (ou tenha conjuntos de dados separados para cada um).
6. Selecione um ou mais algoritmos de aprendizado de máquina dependendo do problema de aprendizado a ser resolvido. Por exemplo, classificação binária, classificação multiclasse, regressão.
7. Treine um ou mais modelos usando o conjunto de informações de treinamento.
8. Pontuar o conjunto de pontos de validação usando os modelos treinados.
9. Avalie os modelos para computar as métricas relevantes para o problema de aprendizado.
10. Ajuste os modelos e selecione o melhor modelo a ser implantado.

Neste exercício, já exploramos e projetamos os dados em SQL Server e decidimos o tamanho da amostra para ingerir Azure Machine Learning. Para criar um ou mais dos modelos de previsão que decidimos:

1. Obtenha os dados para Azure Machine Learning usando o módulo [importar dados][import-data] , disponível na seção **entrada e saída de dados** . Para obter mais informações, consulte a página de referência do módulo [importar dados][import-data] .
   
    ![Azure Machine Learning importar dados][17]
2. Selecione **Azure SQL Database** como a **fonte de dados** no painel **Propriedades** .
3. Insira o nome DNS do banco de dados no campo **nome do servidor de banco de dados** . Formato: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Insira o **nome do banco de dados** no campo correspondente.
5. Insira o **nome de usuário do SQL** no **nome da conta de usuário do servidor**e a **senha** na senha da conta de **usuário do servidor**.
7. Na área de texto Editar **consulta de banco de dados** , Cole a consulta que extrai os campos de banco de dados necessários (incluindo quaisquer campos computados, como os rótulos) e reduza os data para o tamanho de amostra desejado.

Um exemplo de um experimento de classificação binária com a leitura de dados diretamente do SQL Server Database está na figura abaixo. Experimentos semelhantes podem ser construídos para problemas de classificação e regressão de multiclasse.

![Treinamento de Azure Machine Learning][10]

> [!IMPORTANT]
> Nos exemplos de consulta extração de dados de modelagem e amostragem fornecidos nas seções anteriores, **todos os rótulos para os três exercícios de modelagem são incluídos na consulta**. Uma etapa importante (obrigatória) em cada um dos exercícios de modelagem é **excluir** os rótulos desnecessários para os outros dois problemas e quaisquer outros **vazamentos de destino**. Por exemplo, ao usar a classificação binária, use **o rótulo indicado** e exclua os campos **Tip\_classe**, **tip\_valor**e **total\_valor**. Os últimos são vazamentos de destino, pois implicam a gorjeta paga.
> 
> Para excluir colunas desnecessárias e/ou vazamentos de destino, você pode usar o módulo [selecionar colunas no conjunto][select-columns] de módulos ou [Editar metadados][edit-metadata]. Para obter mais informações, consulte [selecionar colunas nas páginas de referência do conjunto][select-columns] de dados e [Editar metadados][edit-metadata] .
> 
> 

## <a name="mldeploy"></a>Implantando modelos no Azure Machine Learning
Quando seu modelo estiver pronto, você poderá implantá-lo facilmente como um serviço Web diretamente do experimento. Para obter mais informações sobre a implantação de serviços Web do Azure Machine Learning, consulte [implantar um serviço Web do Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

Para implantar um novo serviço Web, você precisa:

1. Crie um experimento de pontuação.
2. Implante o serviço Web.

Para criar um experimento de Pontuação de um experimento de treinamento **concluído** , clique em **criar experimento de Pontuação** na barra de ação inferior.

![Pontuação do Azure][18]

Azure Machine Learning tentará criar um experimento de pontuação com base nos componentes do teste de treinamento. Em particular, ele irá:

1. Salve o modelo treinado e remova os módulos de treinamento do modelo.
2. Identifique uma **porta de entrada** lógica para representar o esquema de dados de entrada esperado.
3. Identifique uma **porta de saída** lógica para representar o esquema de saída do serviço Web esperado.

Quando o experimento de pontuação for criado, examine-o e ajuste conforme necessário. Um ajuste típico é substituir o conjunto de dados de entrada e/ou a consulta por um que exclua os campos de rótulo, pois eles não estarão disponíveis quando o serviço for chamado. Também é uma boa prática reduzir o tamanho do conjunto de dados de entrada e/ou da consulta para alguns registros, apenas o suficiente para indicar o esquema de entrada. Para a porta de saída, é comum excluir todos os campos de entrada e incluir apenas os **Rótulos pontuados** e as **probabilidades pontuadas** na saída usando o módulo [selecionar colunas no conjunto][select-columns] de dados.

Um experimento de Pontuação de exemplo está na figura abaixo. Quando estiver pronto para implantar, clique no botão **publicar serviço Web** na barra de ação inferior.

![Azure Machine Learning publicar][11]

Para recapitular, neste tutorial explicativo, você criou um ambiente de ciência de dados do Azure, trabalhou com um grande conjunto de dados público, desde a aquisição até o treinamento de modelo e a implantação de um serviço Web Azure Machine Learning.

### <a name="license-information"></a>Informações de licença
Este exemplo explicativo e seus scripts que os acompanham e os notebooks IPython são compartilhados pela Microsoft sob a licença MIT. Verifique o arquivo LICENSE. txt no diretório do código de exemplo no GitHub para obter mais detalhes.

### <a name="references"></a>Referências
• [Página de download do Andrés MONROY NYC táxi TRIPS](https://www.andresmh.com/nyctaxitrips/)  
• [Frustrando os dados de corrida de táxi de NYC por Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Pesquisa e estatísticas de NYC táxi e limusines Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

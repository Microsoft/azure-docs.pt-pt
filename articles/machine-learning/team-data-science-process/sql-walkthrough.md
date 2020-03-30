---
title: Construir e implementar um modelo num SQL Server VM - Team Data Science Process
description: Construa e implemente um modelo de machine learning utilizando o SQL Server num VM Azure com um conjunto de dados disponível publicamente.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a47f30cf00624faf098c8b605534cf355eacadee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251586"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>O Processo de Ciência de Dados da Equipa em ação: usando o Servidor SQL
Neste tutorial, você percorre o processo de construção e implementação de um modelo de machine learning usando o SQL Server e um conjunto de dados disponível publicamente -- o conjunto de dados de Viagens de Táxi de [NYC.](https://www.andresmh.com/nyctaxitrips/) O procedimento segue um fluxo padrão de trabalho de ciência de dados: ingerir e explorar os dados, desenvolver funcionalidades para facilitar a aprendizagem, em seguida, construir e implementar um modelo.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Descrição do conjunto de dados de viagens de táxi de NYC
Os dados da NyC Taxi Trip são de cerca de 20 GB de ficheiros CSV comprimidos (~48 GB descomprimidos), compreendendo mais de 173 milhões de viagens individuais e as tarifas pagas por cada viagem. Cada registo de viagem inclui o local e hora de recolha e entrega, número de licença de hack anoonymizado (motorista) e número de medalhão (id único do táxi). Os dados abrangem todas as viagens do ano de 2013 e são fornecidos nos dois conjuntos de dados seguintes para cada mês:

1. O CSV 'trip_data' contém detalhes da viagem, tais como o número de passageiros, pontos de recolha e entrega, duração da viagem e duração da viagem. Aqui estão alguns registos de amostras:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. O CSV 'trip_fare' contém detalhes da tarifa paga por cada viagem, tais como tipo de pagamento, valor das tarifas, sobretaxa e impostos, gorjetas e portagens, e o valor total pago. Aqui estão alguns registos de amostras:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave única\_para juntar\_dados de viagem e tarifa\_de viagem é composta pelos campos: medalhão, licença de hack e data de recolha.\_

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exemplos de Tarefas de Previsão
Vamos formular três problemas de previsão com base no valor da *ponta,\_* nomeadamente:

* Classificação binária: Prever se uma gorjeta foi ou não paga para uma viagem, isto é, um valor de *gorjeta\_* superior a $0 é um exemplo positivo, enquanto uma *gorjeta\_* de $0 é um exemplo negativo.
* Classificação multiclasse: Para prever o intervalo de gorjeta paga pela viagem. Dividimos a *quantidade de gorjeta\_* em cinco caixotes ou classes:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
* Tarefa de regressão: Prever o valor da gorjeta paga por uma viagem.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Configurar o ambiente de ciência de dados azure para análise avançada
Como pode ver no guia [Plan Your Environment,](plan-your-environment.md) existem várias opções para trabalhar com o conjunto de dados de Viagens de Táxi de NYC em Azure:

* Trabalhe com os dados em blobs Azure e depois modelo em Azure Machine Learning
* Carregue os dados numa base de dados do SQL Server e, em seguida, modele em Azure Machine Learning

Neste tutorial vamos demonstrar a importação paralela a granel dos dados para um Servidor SQL, exploração de dados, engenharia de funcionalidades e amostragem para baixo usando o SQL Server Management Studio, bem como usando o IPython Notebook. [Os scripts de amostra](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) e [os cadernos IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) são partilhados no GitHub. Um caderno IPython de amostra para trabalhar com os dados em blobs Azure também está disponível no mesmo local.

Para configurar o seu ambiente azure data science:

1. [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md)
2. [Criar um espaço de trabalho azure machine learning](../studio/create-workspace.md)
3. [Forneça uma Máquina Virtual](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)de Ciência de Dados, que fornece um Servidor SQL e um servidor IPython Notebook.
   
   > [!NOTE]
   > Os scripts de amostra e os cadernos IPython serão descarregados para a sua máquina virtual data Science durante o processo de configuração. Quando o script de pós-instalação VM estiver concluído, as amostras estarão na biblioteca de Documentos da Sua VM:  
   > 
   > * Scripts de amostra:`C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Amostra De Cadernos IPython:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   onde `<user_name>` está o nome de login do windows do seu VM. Vamos referir-nos às pastas da amostra como **Scripts de Amostra** e **Cadernos IPython da amostra**.
   > 
   > 

Com base no tamanho do conjunto de dados, na localização da fonte de dados e no ambiente-alvo do Azure selecionado, este cenário é semelhante ao [ \#Cenário 5: Grande conjunto de dados em ficheiros locais, alvo SQL Server em Azure VM](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Obtenha os Dados de Fonte Pública
Para obter o conjunto de dados de Viagens de Táxi de [NYC](https://www.andresmh.com/nyctaxitrips/) a partir da sua localização pública, você pode usar qualquer um dos métodos descritos em Move Data de e para o Armazenamento [De Blob Azure](move-azure-blob.md) para copiar os dados para a sua nova máquina virtual.

Para copiar os dados utilizando o AzCopy:

1. Inicie sessão na sua máquina virtual (VM)
2. Crie um novo diretório no disco de dados do VM (Nota: Não utilize o Disco Temporário que vem com o VM como disco de dados).
3. Numa janela Command Prompt, execute a seguinte linha de comando Azcopy, substituindo <path_to_data_folder> com a sua pasta de dados criada em (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Quando o AzCopy estiver concluído, um total de 24 ficheiros\_CSV com\_fecho (12 para dados de viagem e 12 para tarifa de viagem) devem estar na pasta de dados.
4. Desaperte os ficheiros descarregados. Note a pasta onde residem os ficheiros não comprimidos. Esta pasta será referida como\_o\_\_caminho\><para ficheiros de dados .

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Dados de importação a granel na base de dados do servidor SQL
O desempenho de carregar/transferir grandes quantidades de dados para uma base de dados SQL e consultas subsequentes pode ser melhorado utilizando *tabelas e pontos de vista divididos*. Nesta secção, seguiremos as instruções descritas na Importação de Dados A granel paralelo utilizando tabelas de [partição SQL](parallel-load-sql-partitioned-tables.md) para criar uma nova base de dados e carregar os dados em tabelas divididas em paralelo.

1. Enquanto inicia o seu VM, inicie o **Estúdio de Gestão de Servidores SQL**.
2. Conecte-se utilizando a autenticação do Windows.
   
    ![Ligação SSMS][12]
3. Se ainda não alterou o modo de autenticação do Servidor SQL e criou um novo utilizador de login SQL, abra o ficheiro script denominado **change\_auth.sql** na pasta **Sample Scripts.** Altere o nome de utilizador e a palavra-passe predefinidos. Clique em **Executar** na barra de ferramentas para executar o script.
   
    ![Executar script][13]
4. Verifique e/ou altere a base de dados padrão do Servidor SQL e as pastas de registo para garantir que as bases de dados recém-criadas serão armazenadas num Disco de Dados. A imagem VM do Servidor SQL que está otimizada para cargas de armazenamento de dados é pré-configurada com dados e discos de registo. Se o seu VM não incluiu um Disco de Dados e adicionou novos discos rígidos virtuais durante o processo de configuração vM, altere as pastas predefinidas da seguinte forma:
   
   * Clique à direita no nome do Servidor SQL no painel esquerdo e clique em **Propriedades**.
     
       ![Propriedades do servidor SQL][14]
   * Selecione **Definições** de Base de Dados a partir da lista de **página para** a esquerda.
   * Verifique e/ou altere as **localizações predefinidas da Base** de Dados para as localizações **do Data Disk** à sua escolha. Esta localização é onde novas bases de dados residem se criadas com as definições predefinidas.
     
       ![Predefinições de base de dados SQL][15]  
5. Para criar uma nova base de dados e um conjunto de grupos de ficheiros para segurar as tabelas divididas, abra o script da amostra **criar\_db\_default.sql**. O script criará uma nova base de dados chamada **TaxiNYC** e 12 grupos de ficheiros na localização de dados padrão. Cada grupo de ficheiros\_terá um\_mês de dados de viagem e dados de tarifa de viagem. Modifique o nome da base de dados, se desejar. Clique em **Executar** para executar o script.
6. Em seguida, crie duas mesas de partição, uma para os dados da viagem\_e outra para a tarifa da viagem.\_ Abra o script da amostra **criar\_tabela.sql dividida,\_** que irá:
   
   * Crie uma função de partição para dividir os dados por mês.
   * Crie um esquema de partição para mapear os dados de cada mês para um grupo de ficheiros diferente.
   * Crie duas mesas divididas mapeadas para o\_esquema de partição: a **viagem de nyctaxi\_** terá os dados da viagem e a **tarifa nyctaxi\_** irá conter os dados da tarifa da viagem.\_
     
     Clique em **Executar** para executar o script e criar as tabelas divididas.
7. Na pasta **Sample Scripts,** existem dois scripts PowerShell fornecidos para demonstrar importações paralelas a granel de dados para tabelas Do Servidor SQL.
   
   * **bcp\_\_paralelo genérico.ps1** é um roteiro genérico para dados paralelos de importação a granel em uma tabela. Modifique este script para definir as variáveis de entrada e alvo, conforme indicado nas linhas de comentários no script.
   * **bcp\_\_paralelo nyctaxi.ps1** é uma versão pré-configurada do script genérico e pode ser usado para carregar ambas as tabelas para os dados de Viagens de Táxi de NYC.  
8. Clique no nome do script **paralelo\_\_nyctaxi.ps1** do BCP e clique em **Editar** para abri-lo no PowerShell. Reveja as variáveis predefinidas e modifique de acordo com o nome da base de dados selecionada, pasta de dados de entrada, pasta de registo de alvos e caminhos para os ficheiros de formato de amostra **nyctaxi_trip.xml** e **nytaxi\_fare.xml** (fornecido na pasta **Sample Scripts).**
   
    ![Dados de Importação a granel][16]
   
    Também pode selecionar o modo de autenticação, predefinido é a Autenticação do Windows. Clique na seta verde na barra de ferramentas para correr. O guião lançará 24 operações de importação a granel em paralelo, 12 para cada tabela dividida. Pode monitorizar o progresso da importação de dados abrindo a pasta de dados predefinido do SQL Server, tal como definido acima.
9. O script PowerShell relata os tempos de início e de fim. Quando todas as importações a granel estiverem concluídas, o tempo final é reportado. Verifique a pasta de registo de alvos para verificar se as importações a granel foram bem sucedidas, ou seja, sem erros relatados na pasta de registo de alvos.
10. A sua base de dados está agora pronta para exploração, engenharia de recursos e outras operações, conforme desejado. Uma vez que as tabelas são divididas de acordo com o campo de data de **recolha,\_** as consultas que incluem as condições de data de **recolha\_** na cláusula **WHERE** beneficiarão do regime de partição.
11. No Estúdio de Gestão de **Servidores SQL,** explore a amostra de amostra **\_fornecida consultas.sql**. Para executar qualquer uma das consultas de amostra, realce as linhas de consulta e, em seguida, clique em **Executar** na barra de ferramentas.
12. Os dados das Viagens de Táxi de NYC são carregados em duas mesas separadas. Para melhorar as operações de adesão, é altamente recomendado indexar as tabelas. O script da amostra **cria\_índice dividido.sql\_** cria índices divididos no medalhão de chave de adesão, licença de hack **\_e data de recolha\_**.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Exploração de Dados e Engenharia de Recursos no Servidor SQL
Nesta secção, realizaremos a exploração de dados e a geração de funcionalidades executando consultas SQL diretamente no Estúdio de Gestão de **Servidores SQL** utilizando a base de dados do SQL Server criada anteriormente. Um script de amostra chamado **consultas de\_amostra.sql** é fornecido na pasta Sample **Scripts.** Modifique o script para alterar o nome da base de dados, se for diferente do padrão: **TaxiNYC**.

Neste exercício, iremos:

* Ligue-se ao **Estúdio de Gestão de Servidores SQL** utilizando a Autenticação do Windows ou utilizando a Autenticação SQL e o nome de login E palavra-passe sQL.
* Explore a distribuição de dados de alguns campos em diferentes janelas de tempo.
* Investigue a qualidade dos dados dos campos de longitude e latitude.
* Gere etiquetas de classificação binárias e multiclasse com base na quantidade de **ponta\_**.
* Gere funcionalidades e calcule/compare distâncias de viagem.
* Junte-se às duas tabelas e extraa uma amostra aleatória que será usada para construir modelos.

Quando estiver pronto para seguir para o Azure Machine Learning, pode:  

1. Guarde a consulta final do SQL para extrair e provar os dados e copiar a consulta diretamente num módulo de [dados de importação][import-data] em Aprendizagem automática de Azure, ou
2. Persistir os dados amostrados e projetados que pretende utilizar para a construção de modelos numa nova tabela de bases de dados e utilizar a nova tabela no módulo [de dados de importação][import-data] em Azure Machine Learning.

Nesta secção, vamos guardar a consulta final para extrair e provar os dados. O segundo método é demonstrado na secção de Exploração de Dados e Engenharia de Recursos na secção [IPython Notebook.](#ipnb)

Para uma verificação rápida do número de linhas e colunas nas tabelas povoadas anteriormente com a importação paralela a granel,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medalhão
Este exemplo identifica o medalhão (números de táxi) com mais de 100 viagens num determinado período de tempo. A consulta beneficiaria do acesso à mesa dividido, uma vez que está condicionada pelo regime de partição da data de **recolha\_**. A consulta do conjunto de dados completo também fará uso da tabela dividida e/ou digitalização de índice.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploração: Distribuição de viagem por medalhão e hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação da Qualidade dos Dados: Verifique os registos com longitude e/ou latitude incorretas
Este exemplo investiga se algum dos campos de longitude e/ou latitude contém um valor inválido (os graus radianos devem estar entre -90 e 90), ou tem (0, 0) coordenadas.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: Distribuição de viagens não inclinadas vs. Não Gorjeta
Este exemplo encontra o número de viagens que foram gorjetadas vs. não gorjetanum determinado período de tempo (ou em conjunto completo de dados se cobrir o ano inteiro). Esta distribuição reflete a distribuição do rótulo binário a ser mais tarde utilizada para modelação de classificação binária.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Exploração: Tip Class/Range Distribution
Este exemplo calcula a distribuição das gamas de pontas num determinado período de tempo (ou em conjunto completo de dados se cobrir o ano inteiro). Esta distribuição das classes de etiquetas será mais tarde utilizada para modelação de classificação multiclasse.

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

#### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: Computae e Compare distância de viagem
Este exemplo converte a longitude de recolha e abandono e latitude para pontos de geografia SQL, calcula a distância de viagem usando a diferença de pontos de geografia SQL, e devolve uma amostra aleatória dos resultados para comparação. O exemplo limita os resultados a coordenadas válidas apenas utilizando a consulta de avaliação da qualidade dos dados abordada anteriormente.

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

#### <a name="feature-engineering-in-sql-queries"></a>Engenharia de Recursos em Consultas SQL
As consultas de exploração de conversão de rótulos e geografia também podem ser usadas para gerar rótulos/características removendo a parte de contagem. Exemplos adicionais de engenharia de recursos SQL são fornecidos na [secção Deexploração de Dados e Engenharia de Recursos na secção IPython Notebook.](#ipnb) É mais eficiente executar as consultas de geração de funcionalidades em todo o conjunto de dados ou um grande subconjunto do mesmo usando consultas SQL que funcionam diretamente na instância de base de dados do Servidor SQL. As consultas podem ser executadas no **SQL Server Management Studio,** IPython Notebook, ou em qualquer ferramenta de desenvolvimento ou ambiente que possa aceder à base de dados local ou remotamente.

#### <a name="preparing-data-for-model-building"></a>Preparação de dados para construção de modelos
A seguinte consulta junta-se às tabelas de tarifas da **nyctaxi\_** e da **nyctaxi,\_** gera uma etiqueta de classificação binária **inclinada**, uma classe de **classificação\_** de várias classes, e extrai uma amostra aleatória de 1% do conjunto de dados completo. Esta consulta pode ser copiada e depois colada diretamente no módulo de dados de [importação][import-data] do Estúdio de [Aprendizagem automática Azure](https://studio.azureml.net) para ingestão direta de dados a partir da instância de base de dados do SQL Server em Azure. A consulta exclui registos com coordenadas incorretas (0,0).

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


## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Exploração de Dados e Engenharia de Recursos no Caderno IPython
Nesta secção, realizaremos a exploração de dados e a geração de funcionalidades usando consultas Python e SQL contra a base de dados do SQL Server criada anteriormente. Um caderno IPython de amostra chamado **machine-Learning-data-science-process-sql-story.ipynb** é fornecido na pasta **de Cadernos Sample IPython.** Este caderno também está disponível no [GitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)

Ao trabalhar com big data, siga esta sequência recomendada:

* Leia numa pequena amostra dos dados num quadro de dados na memória.
* Efetue algumas visualizações e explorações utilizando os dados amostrados.
* Experimente a engenharia de recursos utilizando os dados amostrados.
* Para uma maior exploração de dados, manipulação de dados e engenharia de recursos, utilize python para emitir consultas SQL diretamente contra a base de dados do SQL Server no Azure VM.
* Decida o tamanho da amostra para a construção do modelo Azure Machine Learning.

Quando estiver pronto para seguir para o Azure Machine Learning, pode:  

1. Guarde a consulta final do SQL para extrair e provar os dados e copiar a consulta diretamente num módulo de [dados de importação][import-data] em Azure Machine Learning. Este método é demonstrado nos Modelos de Construção na secção [De Aprendizagem automática Azure.](#mlmodel)    
2. Persistir os dados amostrados e projetados que pretende utilizar para a construção de modelos numa nova tabela de bases de dados e, em seguida, utilizar a nova tabela no módulo [dados de importação.][import-data]

Seguem-se alguns exemplos de exploração de dados, visualização de dados e engenharia de recursos. Para mais exemplos, consulte a amostra do caderno SQL IPython na pasta **de Cadernos Sample IPython.**

#### <a name="initialize-database-credentials"></a>Inicializar credenciais de base de dados
Inicialize as definições de ligação da base de dados nas seguintes variáveis:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Criar conexão de base de dados
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Número de relatório de linhas e colunas na tabela nyctaxi_trip
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

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Leia uma pequena amostra de dados da Base de Dados do Servidor SQL
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

Hora de ler a mesa de amostras é de 6.492000 segundos  
Número de linhas e colunas recuperadas = (84952, 21)

#### <a name="descriptive-statistics"></a>Estatísticas Descritivas
Agora estão prontos para explorar os dados amostrados. Começamos por analisar estatísticas descritivas para a distância de **viagem\_** (ou qualquer outra) área(s):

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualização: Box Plot Exemplo
Em seguida, olhamos para o enredo da caixa para a distância da viagem para visualizar os quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Enredo #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualização: Exemplo de Lote de Distribuição
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Lote #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualização: Parcelas de Barras e Linhas
Neste exemplo, colocamos a distância da viagem em cinco caixas e visualizamos os resultados de fixação.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos traçar a distribuição acima do caixote do lixo em um plano de bar ou linha como abaixo

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Enredo #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Lote #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualização: Exemplo de scatterplot
Mostramos enredo de dispersão entre o **tempo\_\_de viagem em\_secs** e a distância da **viagem\_** para ver se há alguma correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Enredo #6][6]

Da mesma forma, podemos verificar a relação entre o **código de tarifas\_** e a distância da **viagem.\_**

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Enredo #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Sub-Amostragem dos dados em SQL
Ao preparar dados para a construção de modelos no [Azure Machine Learning Studio,](https://studio.azureml.net)pode decidir sobre a **consulta SQL para utilizar diretamente no módulo de Dados** de Importação ou persistir os dados projetados e amostrados numa nova tabela, que poderá utilizar no módulo dados de [importação][import-data] com um simples SELECT * FROM <o **\_seu novo\_nome\_ **de mesa>.

Nesta secção, criaremos uma nova tabela para reter os dados amostrados e projetados. Um exemplo de uma consulta SQL direta para a construção de modelos é fornecido na secção de Exploração de Dados e Engenharia de Recursos na secção [SQL Server.](#dbexplore)

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Crie uma Tabela de Amostras e povoe com 1% das Mesas Unidas. Mesa de lançamento primeiro se existir.
Nesta secção, juntamo-nos às mesas **da nytaxi\_trip** e da tarifa **nyctaxi,\_** extraímos uma amostra aleatória de 1% e persistimos os dados amostrados num novo nome de mesa **nyctaxi\_um\_por cento**:

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

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Exploração de dados usando consultas SQL no Notebook IPython
Nesta secção, exploramos as distribuições de dados utilizando os dados 1% amostrados que persiste na nova tabela que criamos acima. Explorações semelhantes podem ser realizadas utilizando as tabelas originais, utilizando opcionalmente **tablesample** para limitar a amostra de exploração ou limitando os resultados a um determinado período de tempo utilizando as divisórias de data de **recolha,\_** como ilustrado na secção de Exploração de Dados e Engenharia de Recursos na secção [SQL Server.](#dbexplore)

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: Distribuição diária de viagens
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: Distribuição de viagem por medalhão
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Geração de recursos usando consultas SQL em Caderno IPython
Nesta secção vamos gerar novos rótulos e funcionalidades diretamente usando consultas SQL, operando na tabela de amostras de 1% que criamos na secção anterior.

#### <a name="label-generation-generate-class-labels"></a>Geração de etiquetas: Gerar rótulos de classe
No exemplo seguinte, geramos dois conjuntos de rótulos para utilizar para modelação:

1. Etiquetas de classe **binárias inclinadas** (prevendo se uma dica será dada)
2. Classe de **ponta\_** multiclasse Labels (previsão do caixote do lixo ou do alcance)
   
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

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Engenharia de Recursos: Características do conde para colunas categóricas
Este exemplo transforma um campo categórico num campo numérico substituindo cada categoria pela contagem das suas ocorrências nos dados.

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

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Engenharia de Recursos: Funcionalidades de bin para colunas numéricas
Este exemplo transforma um campo numérico contínuo em faixas de categoria predefinidas, isto é, transformar campo numérico em um campo categórico.

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

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Engenharia de Recursos: Características de localização de extrato de Latitude Decimal/Longitude
Este exemplo decompõe a representação decimal de um campo de latitude e/ou longitude em vários campos de região de diferentes granularidades, tais como, país/região, cidade, cidade, bloco, etc. Os novos geocampos não estão mapeados para locais reais. Para obter informações sobre o mapeamento de localizações de geocódigo, consulte [bing Maps REST Services](https://msdn.microsoft.com/library/ff701710.aspx).

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

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifique a forma final da tabela acaracterísticada
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Estamos agora prontos para avançar para a modelação e modelação de implantação em [Azure Machine Learning.](https://studio.azureml.net) Os dados estão prontos para qualquer um dos problemas de previsão identificados anteriormente, nomeadamente:

1. Classificação binária: Para prever se uma gorjeta foi ou não paga para uma viagem.
2. Classificação multiclasse: Para prever o intervalo de gorjeta paga, de acordo com as classes previamente definidas.
3. Tarefa de regressão: Prever o valor da gorjeta paga por uma viagem.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Modelos de construção em aprendizagem automática azure
Para iniciar o exercício de modelação, inicie sessão no seu espaço de trabalho Azure Machine Learning. Se ainda não criou um espaço de trabalho de aprendizagem automática, consulte [Create a Azure Machine Learning workspace](../studio/create-workspace.md).

1. Para começar com o Azure Machine Learning, veja o que é o Estúdio de [Aprendizagem automática Azure?](../studio/what-is-ml-studio.md)
2. Inicie sessão no Estúdio de [Aprendizagem automática Azure.](https://studio.azureml.net)
3. A página Studio Home fornece uma grande quantidade de informação, vídeos, tutoriais, links para a Referência de Módulos, e outros recursos. Para mais informações sobre o Azure Machine Learning, consulte o Centro de Documentação de [Aprendizagem automática Azure.](https://azure.microsoft.com/documentation/services/machine-learning/)

Uma experiência típica de treino consiste nos seguintes passos:

1. Crie uma experiência **+NEW.**
2. Obtenha os dados para azure machine learning.
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gerar funcionalidades conforme necessário.
5. Divida os dados em conjuntos de dados de formação/validação/teste (ou tenha conjuntos de dados separados para cada um).
6. Selecione um ou mais algoritmos de aprendizagem automática dependendo do problema de aprendizagem para resolver. Por exemplo, classificação binária, classificação multiclasse, regressão.
7. Treine um ou mais modelos utilizando o conjunto de dados de treino.
8. Marque o conjunto de dados de validação utilizando os modelos treinados.
9. Avaliar os modelos para calcular as métricas relevantes para o problema da aprendizagem.
10. Sintonize os modelos e selecione o melhor modelo para implementar.

Neste exercício, já explorámos e concebemos os dados no SQL Server, e decidimos o tamanho da amostra para ingerir em Azure Machine Learning. Para construir um ou mais dos modelos de previsão, decidimos:

1. Obtenha os dados para o Azure Machine Learning utilizando o módulo [de dados de importação,][import-data] disponível na secção de Entrada e Saída de **Dados.** Para mais informações, consulte a página de referência do módulo [de dados de importação.][import-data]
   
    ![Dados de importação de aprendizagem automática azure][17]
2. Selecione **Base de Dados Azure SQL** como fonte de **dados** no painel **Propriedades.**
3. Introduza o nome DNS da base de dados no campo de nome do **servidor base de dados.** Formato:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Introduza o **nome Base** de Dados no campo correspondente.
5. Introduza o nome de **utilizador SQL** no nome da **conta**do servidor e a **palavra-passe** na **palavra-passe**da conta do servidor .
7. Na área de edição de consulta de base de **dados,** reexa a consulta que extrai os campos de base de dados necessários (incluindo quaisquer campos computacionais, como as etiquetas) e amostras abaixo dos dados para o tamanho da amostra pretendido.

Um exemplo de uma experiência de classificação binária que lê dados diretamente da base de dados do SQL Server está na figura abaixo. Experiências semelhantes podem ser construídas para problemas de classificação multiclasse e regressão.

![Trem de aprendizagem de máquina saquires azure][10]

> [!IMPORTANT]
> Nos exemplos de extração e amostragem de dados de modelação fornecidos em secções anteriores, **todas as etiquetas para os três exercícios de modelação estão incluídas na consulta**. Um passo importante (necessário) em cada um dos exercícios de modelação é **excluir** os rótulos desnecessários para os outros dois problemas, e quaisquer outras fugas de **alvo.** Para, por exemplo, quando utilizar a classificação binária, utilize a etiqueta **inclinada** e exclua a **classe de ponta\_** dos campos, a quantidade de **\_ponta,** e a quantidade **total\_**. Estes últimos são fugas de alvo, uma vez que implicam a gorjeta paga.
> 
> Para excluir colunas desnecessárias e/ou fugas de alvo, pode utilizar as [Colunas Select no][select-columns] módulo Dataset ou nos Metadados de [Edição][edit-metadata]. Para mais informações, consulte [Selecione Colunas em Dataset][select-columns] e edite páginas de referência de [Metadados.][edit-metadata]
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Implementação de modelos em aprendizagem automática azure
Quando o seu modelo estiver pronto, pode facilmente implantá-lo como um serviço web diretamente da experiência. Para obter mais informações sobre a implementação de serviços web Azure Machine Learning, consulte [A implantação de um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

Para implementar um novo serviço web, é necessário:

1. Crie uma experiência de pontuação.
2. Implementar o serviço web.

Para criar uma experiência de pontuação a partir de uma experiência de treino **terminada,** clique em **CREATE SCORING EXPERIMENT** na barra de ação inferior.

![Pontuação Azure][18]

O Azure Machine Learning tentará criar uma experiência de pontuação baseada nos componentes da experiência de treino. Em particular, irá:

1. Guarde o modelo treinado e retire os módulos de treino do modelo.
2. Identifique uma porta de **entrada** lógica para representar o esquema de dados de entrada esperado.
3. Identifique uma porta de **saída** lógica para representar o esquema de saída de serviço web esperado.

Quando a experiência de pontuação for criada, reveja-a e ajuste-a conforme necessário. Um ajuste típico é substituir o conjunto de dados de entrada e/ou consulta por um que exclui os campos de etiqueta, uma vez que estes rótulos não estarão disponíveis no esquema quando o serviço é chamado. É também uma boa prática reduzir o tamanho do conjunto de dados de entrada e/ou consulta a alguns registos, o suficiente para indicar o esquema de entrada. Para a porta de saída, é comum excluir todos os campos de entrada e apenas incluir as **Etiquetas Pontuadas** e **Probabilidades Pontuadas** na saída utilizando as Colunas Select no módulo [Dataset.][select-columns]

Uma experiência de pontuação de amostraestá na figura abaixo. Quando estiver pronto para ser implantado, clique no botão **PUBLISH WEB SERVICE** na barra de ação inferior.

![Publicação de Aprendizagem automática Azure][11]

Para recapitular, neste tutorial de walkthrough, criou um ambiente de ciência de dados Azure, trabalhou com um grande conjunto de dados públicos desde a aquisição de dados até à formação de modelos e implementação de um serviço web Azure Machine Learning.

### <a name="license-information"></a>Informações de Licença
Esta amostra de passagem e os seus scripts de acompanhamento e os cadernos IPython são partilhados pela Microsoft ao abrigo da licença do MIT. Verifique o ficheiro LICENSE.txt no diretório do código da amostra no GitHub para obter mais detalhes.

### <a name="references"></a>Referências
• Página de descarregamento de viagens de [táxi de Andrés Monroy NYC](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing Dados da Viagem](https://chriswhong.com/open-data/foil_nyc_taxi/) de Táxi de NYC por Chris Whong   
• Pesquisa e Estatísticas da Comissão de [Táxis e Limusines](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) da NYC

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

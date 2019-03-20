---
title: Criar e implementar um modelo numa VM do SQL Server - Team Data Science Process
description: Criar e implementar um modelo de machine learning com o SQL Server numa VM do Azure com um conjunto de dados publicamente disponível.
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
ms.openlocfilehash: c9d707d1a76b3b5913d66745767df8e84362a192
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890863"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>O processo de ciência de dados de equipa em ação: utilizar o SQL Server
Neste tutorial, é necessário percorrer o processo de criação e implementação de um modelo de aprendizagem automática com o SQL Server e um conjunto de dados publicamente disponível o [NYC táxis viagens](https://www.andresmh.com/nyctaxitrips/) conjunto de dados. O procedimento segue um fluxo de trabalho de ciência de dados padrão: ingerir e explorar os dados, funcionalidades de engenharia para facilitar a aprendizagem, em seguida, criar e implementar um modelo.

## <a name="dataset"></a>Descrição do conjunto de dados automático passar NYC táxis
Os dados da viagem de táxis de NYC são cerca de 20GB de arquivos compactados de CSV (GB de ~ 48 descomprimido), que consiste em mais de 173 milhões de viagens individuais e os fares pago para cada viagem. Cada registo de viagem inclui a localização de recolha e de redução e tempo, hack anónimo (driver) número de licença e o número de medallion (id exclusivo de táxis). Os dados abrange todas as viagens no ano de 2013 e são fornecidos no seguintes dois conjuntos de dados para todos os meses:

1. O 'trip_data CSV contém detalhes de viagem, como o número de passageiros, recolha e pontos de redução, duração de viagem e comprimento de viagem. Aqui estão alguns exemplos de registros:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. O trip_fare CSV contém detalhes de Europeia pago para cada viagem, como o tipo de pagamento, a quantidade de Europeia, sobretaxa e impostos, dicas e pedágio e o valor total pago. Aqui estão alguns exemplos de registros:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para aderir a viagem\_dados e viagem\_Europeia é composta pelos campos: medallion, aceder de modo ilícito\_licença e recolha\_datetime.

## <a name="mltasks"></a>Exemplos de tarefas de predição
Podemos irá formular três problemas de previsão com base na *sugestão\_quantidade*, ou seja:

1. Classificação binária: Prever se ou não uma dica foi paga por uma viagem, ou seja, um *sugestão\_quantidade* superior de US $0 é um exemplo positivo, enquanto uma *tip\_quantidade* de US $0 é um exemplo negativo.
2. Classificação de várias classes: Para prever o intervalo de sugestão pagos para a viagem. Vamos dividir o *sugestão\_quantidade* em cinco contentores ou classes:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Tarefa de regressão: Para prever a quantidade de sugestão pagos por uma viagem.  

## <a name="setup"></a>Ambiente de ciência de dados de configuração cópia de segurança do Azure para análise avançada
Como pode ver a partir do [planear o ambiente](plan-your-environment.md) guia, há várias opções para trabalhar com o conjunto de dados de viagens de táxis de NYC no Azure:

* Trabalhar com os dados em blobs do Azure, em seguida, o modelo no Azure Machine Learning
* Carregar os dados para uma base de dados do SQL Server, em seguida, o modelo no Azure Machine Learning

Neste tutorial, iremos demonstrar importação em massa paralela dos dados para um SQL Server, exploração de dados, a funcionalidade de engenharia e para baixo de amostragem com o SQL Server Management Studio, bem como utilizar o IPython Notebook. [Scripts de exemplo](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) e [IPython notebooks](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) compartilhados no GitHub. Um bloco de notas de IPython de exemplo para trabalhar com os dados em blobs do Azure também está disponível na mesma localização.

Para configurar o ambiente de ciência de dados do Azure:

1. [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md)
2. [Criar uma área de trabalho do Azure Machine Learning](../studio/create-workspace.md)
3. [Aprovisionar uma máquina de Virtual de ciência de dados](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), que fornece um SQL Server e servidor IPython Notebook.
   
   > [!NOTE]
   > Os scripts de exemplo e IPython notebooks serão transferidos para a sua máquina de virtual de ciência de dados durante o processo de configuração. Quando tiver concluído o script de pós-instalação de VM, os exemplos será na biblioteca de documentos da sua VM:  
   > 
   > * Scripts de exemplo: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Blocos de notas do exemplo IPython: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   onde `<user_name>` é o nome de início de sessão do Windows da sua VM. Nós nos referiremos às pastas de exemplo como **Scripts de exemplo** e **blocos de notas do exemplo IPython**.
   > 
   > 

Com base no tamanho do conjunto de dados, localização de origem de dados e o ambiente de destino do Azure selecionado, este cenário é semelhante à [cenário \#5: SQL Server na VM do Azure de destino do conjunto de dados grandes num local de arquivos,](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Obter os dados de origem pública
Para obter o [NYC táxis viagens](https://www.andresmh.com/nyctaxitrips/) conjunto de dados da localização pública, pode utilizar qualquer um dos métodos descritos [mover dados de e para armazenamento de Blobs do Azure](move-azure-blob.md) para copiar os dados para a sua nova máquina virtual.

Para copiar os dados com AzCopy:

1. Inicie sessão na sua máquina virtual (VM)
2. Criar um novo diretório no disco de dados da VM (observação: Não utilize o disco temporário que vem com a VM como um disco de dados).
3. Numa janela de linha de comandos, execute a seguinte linha de comando do Azcopy substituir < path_to_data_folder > a sua pasta de dados criada no (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Quando tiver concluído o AzCopy, um total de 24 comprimido ficheiros CSV (12 da viagem\_dados e 12 para viagem\_Europeia) deve estar na pasta de dados.
4. Descompacte os ficheiros transferidos. Tenha em atenção a pasta onde residem os ficheiros descomprimidos. Esta pasta irá ser referida como o < caminho\_para\_dados\_ficheiros\>.

## <a name="dbload"></a>Dados de importação em massa na base de dados do SQL Server
O desempenho de carregamento/transferência de grandes quantidades de dados para uma base de dados SQL e consultas subsequentes pode ser melhorado usando *vistas e tabelas Particionadas*. Nesta secção, vamos seguir as instruções descritas [paralelas em massa dados importação usando SQL tabelas de partição](parallel-load-sql-partitioned-tables.md) para criar uma nova base de dados e carregar os dados para tabelas particionadas em paralelo.

1. Com sessão iniciada sua VM, iniciar **SQL Server Management Studio**.
2. Ligue-se de utilizar a autenticação do Windows.
   
    ![Ligação SSMS][12]
3. Se ainda não tiver alterado o modo de autenticação do SQL Server e criado um novo utilizador de início de sessão SQL, abra o ficheiro de script denominado **alterar\_auth.sql** no **Scripts de exemplo** pasta. Altere o nome de utilizador predefinido e a palavra-passe. Clique em **! Executar** na barra de ferramentas para executar o script.
   
    ![Executar Script][13]
4. Certifique-se de e/ou alterar os SQL Server da base de dados e de registo pastas predefinidas para se certificar de que serão armazenadas num disco de dados recém-criadas bases de dados. A imagem de VM do SQL Server que está otimizada para cargas de datawarehousing é previamente configurada com discos de dados e de registo. Se a VM não incluía um disco de dados e adicionados novos discos rígidos virtuais durante o processo de configuração VM, altere as pastas padrão da seguinte forma:
   
   * Faça duplo clique o nome do servidor de SQL no painel esquerdo e clique em **propriedades**.
     
       ![Propriedades do SQL Server][14]
   * Selecione **definições de base de dados** partir a **selecionar uma página** lista à esquerda.
   * Certifique-se de e/ou alterar o **localizações predefinidas de base de dados** para o **disco de dados** localizações da sua preferência. Isso é onde residem os novos bancos de dados se criado com as predefinições de localização.
     
       ![Predefinições de base de dados SQL][15]  
5. Para criar uma nova base de dados e um conjunto de grupos de arquivos para armazenar as tabelas particionadas, abra o script de exemplo **crie\_db\_default.sql**. O script irá criar uma nova base de dados com o nome **TaxiNYC** e 12 grupos de ficheiros na localização de dados predefinida. Cada grupo de ficheiros irá conter um mês de viagem\_dados e viagem\_se comportarão de dados. Modifique o nome de base de dados, se assim o desejar. Clique em **! Executar** para executar o script.
6. Em seguida, crie duas tabelas de partição, uma para a viagem\_dados e outro para a viagem\_Europeia. Abra o script de exemplo **crie\_particionada\_table.sql**, que será:
   
   * Crie uma função de partição para dividir os dados por mês.
   * Crie um esquema de partição para mapear dados de cada mês para um grupo de ficheiros diferente.
   * Criar duas tabelas particionadas mapeadas para o esquema de partição: **nyctaxi\_viagem** manterá a viagem\_dados e **nyctaxi\_Europeia** manterá a viagem\_se comportarão de dados.
     
     Clique em **! Executar** para executar o script e criar as tabelas particionadas.
7. Na **Scripts de exemplo** pasta, há dois scripts do PowerShell de exemplo fornecidos para demonstrar em massa paralela importa de dados para tabelas do SQL Server.
   
   * **BCP\_paralela\_generic.ps1** é um script genérico para dados de importação em massa paralela numa tabela. Modificar esse script para definir as variáveis de entrada e de destino, conforme indicado nas linhas de comentário no script.
   * **BCP\_paralela\_nyctaxi.ps1** é uma versão pré-configurada do script genérico e pode ser usado para carregar a ambas as tabelas para os dados de viagens de táxis de NYC.  
8. Com o botão direito a **bcp\_paralela\_nyctaxi.ps1** nome do script e clique em **editar** para abri-lo no PowerShell. Reveja as variáveis predefinidas e modificar de acordo com seu nome de base de dados selecionada, a pasta de dados de entrada, a pasta de registo de destino e a caminhos para os ficheiros de formato de exemplo **nyctaxi_trip.xml** e **nyctaxi\_fare.xml** (fornecidas a **Scripts de exemplo** pasta).
   
    ![Dados de importação em massa][16]
   
    Também pode selecionar o modo de autenticação, a predefinição é a autenticação do Windows. Clique na seta verde na barra de ferramentas para executar. O script iniciará 24 operações de importação de em massa em paralelo, 12 para cada tabela particionada. Pode monitorar o progresso da importação de dados, abrindo a pasta de dados do SQL Server predefinida conforme definido acima.
9. O script do PowerShell os relatórios de horas de início e fim. Quando tudo em massa imports concluída, é comunicado o tempo final. Verifique a pasta de registo de destino para verificar que as importações em massa foram bem-sucedidas, ou seja, não existem erros relatados na pasta de registo de destino.
10. A base de dados está agora pronto para exploração, engenharia de funcionalidades e outras operações conforme pretendido. Uma vez que as tabelas são particionadas de acordo com o **recolha\_datetime** campo, consultas que incluem **recolha\_datetime** condições no **onde** cláusula irá beneficiar o esquema de partição.
11. Na **SQL Server Management Studio**, explore o script de exemplo fornecido **exemplo\_queries.sql**. Para executar qualquer um dos exemplos de consultas, destacar as linhas de consulta, em seguida, clique em **! Executar** na barra de ferramentas.
12. Os dados de viagens de táxis de NYC são carregados em duas tabelas separadas. Para melhorar as operações de associação, é altamente recomendado para as tabelas de índice. O script de exemplo **crie\_particionada\_index.sql** cria índices particionados na chave de junção compostos **medallion, aceder de modo ilícito\_licença e recolha\_ DateTime**.

## <a name="dbexplore"></a>Exploração de dados e de engenharia de funcionalidades no SQL Server
Nesta secção, iremos efetuar geração de exploração e a funcionalidade de dados ao executar consultas SQL diretamente na **SQL Server Management Studio** utilizando a base de dados do SQL Server criado anteriormente. Um script de exemplo chamado **amostra\_queries.sql** é fornecido no **Scripts de exemplo** pasta. Modificar o script para alterar o nome de base de dados, se for diferente da predefinição: **TaxiNYC**.

Neste exercício, iremos:

* Ligar à **SQL Server Management Studio** com a autenticação do Windows ou utilizando a autenticação do SQL e o nome de início de sessão SQL e a palavra-passe.
* Explore as distribuições de dados de alguns campos em várias janelas de tempo.
* Investigue a qualidade dos dados dos campos de longitude e latitude.
* Gerar etiquetas de classificação binária e várias classes com base na **sugestão\_quantidade**.
* Gerar recursos e a computação/compare distâncias de viagem.
* Junte-se as duas tabelas e extraia uma amostra aleatória que será utilizada para criar modelos.

Quando estiver pronto para avançar para o Azure Machine Learning, pode:  

1. Guardar a consulta SQL final para extrair e os dados de exemplo e copiar-colar a consulta diretamente para um [importar dados] [ import-data] módulo no Azure Machine Learning, ou
2. Manter a amostragem e tabela de dados de engenharia que planeia utilizar para o modelo de criação de uma nova base de dados e utilizam a nova tabela no [importar dados] [ import-data] módulo no Azure Machine Learning.

Nesta secção, salvaremos a consulta final para extrair e os dados de exemplo. O segundo método é demonstrado a [exploração de dados e a funcionalidade de engenharia no IPython Notebook](#ipnb) secção.

Para uma verificação rápida do número de linhas e colunas nas tabelas anteriormente utilizando a importação em massa paralela,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medallion
Neste exemplo identifica o medallion (números de táxis) com mais de 100 viagens dentro de um determinado período de tempo. A consulta poderia tirar proveito do acesso a tabela particionada, uma vez que ele está condicionada ao esquema de partição da **recolha\_datetime**. Consultar o conjunto de dados completo também fará com que utilize da tabela particionada e/ou análise de índice.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploração: Distribuição de viagem por medallion e hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação de qualidade de dados: Certifique-se de registos com longitude incorreto e/ou latitude
Neste exemplo investiga se qualquer um dos campos de longitude e/ou latitude optar por conter um valor inválido (radian graus devem estar entre -90 e 90), ou tem (0, 0) coordenadas.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: Colocado para vs. Distribuição de viagens colocado para não
Neste exemplo localiza o número de viagens que foram tipados versus não tipados num tempo determinado período (ou no conjunto de dados completo se abrangendo o ano completo). Essa distribuição reflete a distribuição de etiqueta de binário para ser utilizado mais tarde para a Modelagem de classificação binária.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Exploração: Sugestão de distribuição de classe/intervalo
Neste exemplo calcula a distribuição de intervalos de sugestão num determinado período de tempo (ou no conjunto de dados completo se abrangendo o ano completo). Esta é a distribuição das classes de etiqueta que será utilizado mais tarde para a Modelagem de classificação multiclasses.

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

#### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: Computação e Compare a distância de viagem
Neste exemplo converte a recolha e de redução de longitude e latitude para geografia SQL aponta, computa a distância de viagem com a diferença de pontos de geografia SQL e devolve uma amostra aleatória dos resultados para comparação. O exemplo limita os resultados para as coordenadas válidos apenas usando a consulta de avaliação de qualidade de dados abordada anteriormente.

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

#### <a name="feature-engineering-in-sql-queries"></a>Engenharia de funcionalidades nas consultas SQL
A etiqueta de geração e geografia conversão exploração consultas pode também ser usada para gerar etiquetas/funcionalidades removendo a parte de contagem. São fornecidos exemplos SQL engenharia de funcionalidades adicionais da [exploração de dados e a funcionalidade de engenharia no IPython Notebook](#ipnb) secção. É mais eficiente para executar a funcionalidade de consultas de geração no conjunto de dados completo ou de um grande subconjunto do mesmo através de consultas SQL que são executadas diretamente na instância de base de dados do SQL Server. As consultas podem ser executadas em **SQL Server Management Studio**, IPython Notebook ou qualquer ferramenta/ambiente de desenvolvimento que podem aceder a base de dados, localmente ou remotamente.

#### <a name="preparing-data-for-model-building"></a>Preparando dados para a criação de modelo
A seguinte consulta associa a **nyctaxi\_viagem** e **nyctaxi\_Europeia** tabelas, gera uma etiqueta de classificação binária **colocado para**, um etiqueta de classificação de Roc **sugestão\_classe**e extrai uma amostra aleatória de 1% do conjunto de dados associado ao completo. Esta consulta pode ser copiada e colada diretamente no [Azure Machine Learning Studio](https://studio.azureml.net) [importar dados] [ import-data] módulo direto para ingestão de dados da base de dados do SQL Server instância no Azure. A consulta exclui registos com incorreto (0, 0) coordenadas.

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


## <a name="ipnb"></a>Exploração de dados e de engenharia de funcionalidades no IPython Notebook
Nesta secção, iremos efetuar exploração de dados e a geração de recursos através de consultas de Python e o SQL na base de dados do SQL Server criado anteriormente. Um bloco de notas de IPython do exemplo com o nome **machine-Learning-data-science-process-sql-story.ipynb** é fornecido na **blocos de notas do exemplo IPython** pasta. Este bloco de notas também está disponível no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

A sequência de recomendada ao trabalhar com grandes volumes de dados é o seguinte:

* Leia-se numa pequena amostra dos dados num quadro de dados na memória.
* Efetue algumas visualizações e explorations usando os dados de amostras.
* Experimentar a engenharia de funcionalidades com os dados de amostras.
* Para exploração de dados maior, a manipulação de dados e a engenharia de funcionalidades, utilize o Python para emitir consultas SQL diretamente contra a base de dados do SQL Server na VM do Azure.
* Decida o tamanho de exemplo para utilizar para a criação de modelo do Azure Machine Learning.

Quando estiver pronto para avançar para o Azure Machine Learning, pode:  

1. Guardar a consulta SQL final para extrair e os dados de exemplo e copiar-colar a consulta diretamente para um [importar dados] [ import-data] módulo no Azure Machine Learning. Este método é demonstrado a [modelos de construção no Azure Machine Learning](#mlmodel) secção.    
2. Manter os dados de amostras e engenharia que planeia utilizar para o modelo de criação de uma nova tabela de base de dados, em seguida, utilize a nova tabela no [importar dados] [ import-data] módulo.

Seguem-se alguns exploração de dados, visualização de dados e funcionalidade de exemplos de engenharia. Para obter mais exemplos, consulte o exemplo SQL IPython notebook no **blocos de notas do exemplo IPython** pasta.

#### <a name="initialize-database-credentials"></a>Inicializar as credenciais da base de dados
Inicialize as definições de ligação de base de dados nas seguintes variáveis:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Criar ligação de base de dados
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Relatar o número de linhas e colunas na tabela nyctaxi_trip
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

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Leia-num exemplo de dados pequenas da base de dados do SQL Server
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

Tempo de leitura que a tabela de exemplo é 6.492000 segundos  
Número de linhas e colunas obter = (84952, 21)

#### <a name="descriptive-statistics"></a>Estatísticas descritivas
Agora está pronto para explorar os dados de amostras. Vamos começar com observar estatísticas descritivas para o **viagem\_distância** (ou qualquer outro) campos:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualização: Exemplo de plotagem de caixa
Em seguida, observe o desenho de caixa para a distância de viagem visualizar o quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Desenhar #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualização: Exemplo de plotagem de distribuição
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Desenhar #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualização: Barras e gráficos de linha
Neste exemplo, a distância de viagem para cinco contentores de discretização e visualizar os resultados de discretização.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos pode representar a distribuição de bin acima numa barra ou linha de plotagem, conforme mostrado a seguir

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![#3 de desenho][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Desenhar #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualização: Exemplo de gráfico de dispersão
Vamos mostrar gráfico de dispersão entre **viagem\_tempo\_no\_segundos** e **viagem\_distância** para ver se há qualquer correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Desenhar #6][6]

Da mesma forma podemos verificar a relação entre **taxa\_código** e **viagem\_distância**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Desenhar #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Os dados em SQL de amostragem inferiores
Quando preparar dados para o modelo a criar no [Azure Machine Learning Studio](https://studio.azureml.net), que pode optar por opte por utilizar o **consulta SQL a utilizar diretamente no módulo importar dados** ou persistir os dados de engenharia e amostras numa nova tabela, que pode ser usado na [importar dados] [ import-data] módulo com um simples **SELECIONAR * FROM < sua\_novo\_tabela\_nome >** .

Nesta secção, irá criar uma nova tabela para armazenar os dados de amostras e engenharia. Um exemplo de uma consulta SQL direto para a criação de modelo é fornecido na [exploração de dados e a funcionalidade de engenharia no SQL Server](#dbexplore) secção.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Criar um exemplo de tabela e preencher com 1% das tabelas associadas. Remova tabela primeiro se existir.
Nesta secção, vamos associar as tabelas **nyctaxi\_viagem** e **nyctaxi\_Europeia**extrair uma amostra aleatória de 1% e persistir os dados de amostras num novo nome de tabela  **nyctaxi\_um\_por cento**:

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

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Exploração de dados através de consultas de SQL no IPython Notebook
Nesta secção, vamos explorar as distribuições de dados usando os dados de 1% amostragem que são persistidos na nova tabela que criámos acima. Tenha em atenção que podem ser executadas explorations semelhante usando as tabelas originais, utilizando opcionalmente **TABLESAMPLE** para limitar a exploração de exemplo ou ao limitar os resultados para um determinado momento período usando o **recolha\_datetime** particiona, conforme ilustrado na [exploração de dados e a funcionalidade de engenharia no SQL Server](#dbexplore) secção.

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: Distribuição diária de viagens
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: Distribuição de viagem por medallion
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Geração de recursos através de consultas SQL no IPython Notebook
Nesta secção, irá gerar novas etiquetas e recursos diretamente através de consultas SQL, operar na tabela 1% exemplo que criámos na secção anterior.

#### <a name="label-generation-generate-class-labels"></a>Geração de etiqueta: Gerar etiquetas de classe
No exemplo a seguir, vamos gerar dois conjuntos de etiquetas a utilizar para a Modelagem de:

1. Etiquetas de classe binário **colocado para** (prever se uma dica terá a chance)
2. Etiquetas de várias classes **sugestão\_classe** (prever o bin sugestão ou intervalo)
   
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

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Engenharia de funcionalidades: Funcionalidades de contagem de colunas Categóricas
Neste exemplo transforma um campo categórico num campo numérico, substituindo cada categoria com a contagem de seu ocorrências nos dados.

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

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Engenharia de funcionalidades: Funcionalidades de bin para colunas numéricas
Neste exemplo transforma um campo numérico contínuo em intervalos de categoria predefinidas, ou seja, transformação campo numérico num campo categórico.

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

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Engenharia de funcionalidades: Extrair funcionalidades de localização de Decimal Latitude/Longitude
Neste exemplo divide a representação decimal de um campo de latitude e/ou longitude em vários campos de região de granularidade diferente, como, país, cidade, cidade, bloquear, etc. Tenha em atenção que os novos campos de geo não são mapeados para localizações reais. Para obter informações sobre localizações de geocode de mapeamento, consulte [serviços de REST do Bing Maps](https://msdn.microsoft.com/library/ff701710.aspx).

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

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifique se o formulário final da tabela caracterizadas
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Vamos agora está prontos para avançar para a criação de modelo e implementação de modelo na [do Azure Machine Learning](https://studio.azureml.net). Os dados estão prontos para os problemas de predição identificadas anteriormente, ou seja:

1. Classificação binária: Para prever se é ou não uma dica foi paga por uma viagem.
2. Classificação de várias classes: Para prever o intervalo de sugestão paga, de acordo com as classes definidas anteriormente.
3. Tarefa de regressão: Para prever a quantidade de sugestão pagos por uma viagem.  

## <a name="mlmodel"></a>Modelos de construção no Azure Machine Learning
Para iniciar o exercício de modelagem, inicie sessão na sua área de trabalho do Azure Machine Learning. Se ainda não tiver criado um área de trabalho de aprendizagem automática, consulte [criar uma área de trabalho do Azure Machine Learning](../studio/create-workspace.md).

1. Para começar a utilizar com o Azure Machine Learning, consulte o artigo [o que é o Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Inicie sessão no [do Azure Machine Learning Studio](https://studio.azureml.net).
3. A página inicial do Studio fornece uma grande quantidade de informações, vídeos, tutoriais, links para a referência de módulos e outros recursos. Obter mais informações sobre o Azure Machine Learning, consulte a [Centro de documentação do Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Uma experimentação de preparação típico consiste no seguinte:

1. Criar uma **+ novo** experimentar.
2. Obter os dados para o Azure Machine Learning.
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gere recursos conforme necessário.
5. Dividir os dados em conjuntos de dados de treinamento/validação/teste (ou tem conjuntos de dados separados para cada um).
6. Selecione um ou mais algoritmos de machine learning consoante o problema de aprendizagem para resolver. Por exemplo, classificação binária, classificação de várias classes, regressão.
7. Prepare um ou mais modelos com o conjunto de dados de treinamento.
8. Classificar o conjunto de dados de validação utilizando o modelo treinado (s).
9. Avalie o modelo (s) para calcular as métricas relevantes para o problema de aprendizagem.
10. Bem, Otimize o modelo (s) e selecionar o melhor modelo para implementar.

Neste exercício, temos já explorou e desenvolvido os dados no SQL Server e decidir o tamanho da amostra para ingerir no Azure Machine Learning. Para criar um ou mais dos modelos de previsão, decidimos:

1. Obter os dados para o Azure Machine Learning utilizando o [importar dados] [ import-data] módulo, disponível na **dados de entrada e saída** secção. Para obter mais informações, consulte a [importar dados] [ import-data] página de referência do módulo.
   
    ![Importar dados do Azure Machine Learning][17]
2. Selecione **Azure SQL Database** como o **origem de dados** no **propriedades** painel.
3. Introduza o nome DNS de base de dados na **nome do servidor de base de dados** campo. Formato: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Introduza o **nome da base de dados** o campo correspondente.
5. Introduza o **nome de utilizador SQL** no **nome de conta de utilizador do servidor**e o **palavra-passe** no **palavra-passe de conta de utilizador servidor**.
7. Na **consulta de base de dados** editar área de texto, cole a consulta que extrai os campos de banco de dados necessários (incluindo quaisquer campos calculados, como os rótulos) e baixo exemplos os dados para o tamanho da amostra pretendido.

Um exemplo de uma experimentação de classificação binária ler os dados diretamente a partir da base de dados do SQL Server é na imagem abaixo. Experiências semelhantes podem ser construídas para a classificação de várias classes e problemas de regressão.

![O Azure Machine Learning Train][10]

> [!IMPORTANT]
> Os dados de modelagem de extração e fazendo a amostragem de exemplos de consulta fornecidos nas secções anteriores, **todas as etiquetas para os três exercícios de modelagem são incluídas na consulta**. É um passo importante (obrigatório) em cada um dos exercícios de modelagem **excluir** as etiquetas desnecessárias para os outros dois problemas e qualquer outro **vazamentos de destino**. Para por exemplo, ao utilizar a classificação binária, utilize a etiqueta **tipados** e excluir os campos **sugestão\_classe**, **tip\_quantidade**e **total\_quantidade**. Essas últimas são vazamentos de destino, uma vez que eles implicam a dica pago.
> 
> Para excluir colunas desnecessárias e/ou vazamentos de destino, pode utilizar o [Select Columns in Dataset] [ select-columns] módulo ou o [Editar metadados][edit-metadata]. Para obter mais informações, consulte [Select Columns in Dataset] [ select-columns] e [Editar metadados] [ edit-metadata] páginas de referência.
> 
> 

## <a name="mldeploy"></a>Implementação de modelos no Azure Machine Learning
Quando o modelo estiver pronto, pode implementá-la facilmente como um serviço web diretamente a partir da experimentação. Para obter mais informações sobre a implementação de serviços web Azure Machine Learning, consulte [implementar um serviço web do Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

Para implementar um novo serviço web, terá de:

1. Crie uma experimentação de classificação.
2. Implemente o serviço web.

Criar uma experiência de classificação de um **concluído** experimentação de preparação, clique em **criar classificação EXPERIMENTAR** na barra de ação mais baixa.

![Classificação do Azure][18]

O Azure Machine Learning irá tentar criar uma experiência de classificação com base nos componentes da experimentação de preparação. Em particular, irá:

1. Guardar o modelo preparado e remova os módulos de treinamento de modelo.
2. Identificar uma lógica **porta de entrada** para representar o esquema de dados de entrada esperado.
3. Identificar uma lógica **porta de saída** para representar o esquema de saída do serviço web esperado.

Quando a experimentação de classificação é criada, reveja-la e ajustar conforme necessário. Um ajuste de típico é substituir o conjunto de dados de entrada e/ou a consulta com um que exclui os campos de etiqueta, à medida que estes não estarão disponíveis quando o serviço é chamado. Também é uma boa prática para reduzir o tamanho do conjunto de dados de entrada e/ou a consulta para alguns registos, apenas o suficiente para indicar o esquema de entrada. Para a porta de saída, é comum para excluir campos de entrada tudo e incluir apenas os **etiquetas classificadas** e **probabilidades classificadas** no saída com o [selecionar colunas no conjunto de dados] [ select-columns] módulo.

É um exemplo de experimentação de classificação na imagem abaixo. Quando estiver pronto para implementar, clique nas **publicar WEB SERVICE** botão na barra de ação mais baixo.

![Publicar do Azure Machine Learning][11]

Para recapitular, neste tutorial passo a passo, criou um ambiente de ciência de dados do Azure, trabalhado com um grande conjunto de dados público desde a aquisição de dados para modelar a formação e a implantação de um serviço web Azure Machine Learning.

### <a name="license-information"></a>Informações de licença
Estas instruções de exemplo e que o acompanha scripts e o IPython notebook(s) são partilhadas pela Microsoft sob a licença do MIT. Verifique o ficheiro de LICENSE.txt no diretório do código de exemplo no GitHub para obter mais detalhes.

### <a name="references"></a>Referências
• [Andrés Monroy NYC táxis viagens a página de transferência](https://www.andresmh.com/nyctaxitrips/)  
• [Táxis de fOILing NYC dados de viagens por Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Comissão de táxis de NYC e Limousine pesquisa e as estatísticas](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
